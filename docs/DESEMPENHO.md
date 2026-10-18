# DESEMPENHO.md

# Desempenho — GastroSynq

## Visão Geral

Num restaurante, o sistema é usado com fila no caixa e pedido saindo na cozinha: cada segundo de espera aparece. Por isso, a fase final do projeto foi dedicada a deixar o GastroSynq — e principalmente a HarpIA — o mais rápido possível.

A abordagem foi **medir antes de otimizar**:

1. Instrumentar o backend para registrar o tempo de cada requisição.
2. Identificar onde o tempo realmente era gasto.
3. Atacar os maiores gargalos primeiro.
4. Medir de novo em produção.

---

# Medição

O filtro `TempoRequisicaoFiltro` registra no log o tempo de **toda** requisição HTTP:

```text
[Tempo] GET /estoque/listar 92 ms
[Tempo] POST /auth/login 179 ms
[Lento] POST /agente/perguntar 3705 ms (status 200)
```

* Requisições acima de um limite (padrão 800 ms, variável `TEMPO_LENTO_MS`) aparecem como **`[Lento]`**, em nível de alerta.
* O WebSocket é ignorado, pois fica aberto continuamente.
* Para consultar em produção:

```bash
docker logs gastrosynq-backend --since 5m 2>&1 | grep -E "\[Tempo\]|\[Lento\]"
```

---

# Gargalos Encontrados e Soluções

## 1. Distância entre servidor e banco

**Problema:** a VM ficava em São Paulo e o banco em Ohio (EUA). Cada consulta ao banco fazia uma viagem de ida e volta de ~120–150 ms. Uma tela com 5 consultas perdia quase 1 segundo só em rede.

**Solução:** migração do banco para a região de São Paulo (`sa-east-1`), a mesma da VM, com `pg_dump`/`psql` (ver [INFRAESTRUTURA.md](INFRAESTRUTURA.md)).

**Resultado:** a viagem caiu para poucos milissegundos; foi a otimização de maior impacto percebido.

## 2. Consultas N+1 (Hibernate)

**Problema:** relacionamentos `@ManyToOne` carregados de forma *eager* faziam o Hibernate disparar uma consulta extra para cada item de uma lista (ex.: o produto de cada lote do estoque).

**Solução:** `hibernate.default_batch_fetch_size=50` — o Hibernate passa a buscar os relacionamentos em lotes de até 50 com `IN (...)`, em vez de um por um.

## 3. Pool de conexões instável

**Problema:** o pooler do Supabase limita 15 conexões por sessão. Conexões abandonadas ocupavam as vagas, e abrir conexões sob demanda adicionava latência.

**Solução:** HikariCP com **4 conexões fixas** sempre abertas (`minimum-idle` = máximo), `keepalive` de 120 s e renovação a cada 10 min.

## 4. Autenticação consultando o banco a cada requisição

**Problema:** o `JwtFiltro` buscava o funcionário no banco em toda requisição, para checar se continuava ativo.

**Solução:** cache de 30 s do funcionário autenticado. O cache é limpo imediatamente quando um funcionário é editado ou desativado, então a segurança não é afetada.

## 5. SQL no log

**Problema:** `show-sql=true` escrevia cada consulta no log, gastando CPU e disco.

**Solução:** desligado por padrão (`SHOW_SQL=false`), ativável só para depuração.

## 6. Chamadas desnecessárias ao modelo de IA

Cada chamada ao Gemini custa de 1 a 3 segundos. As otimizações da HarpIA eliminam chamadas sempre que possível:

| Situação | Antes | Depois |
| --- | --- | --- |
| Pedido de lista ("o que tem no estoque?") | 2 chamadas | 1 chamada + formatação local |
| Alteração (venda, entrada...) | 2 chamadas | 1 chamada + resposta padronizada |
| Erro de regra (produto não encontrado) | 2 chamadas | 1 chamada + mensagem do backend |
| Venda de 3 itens diferentes | 3 confirmações | 1 confirmação |

Além disso:

* Declaração das ferramentas montada **uma vez** e mantida em memória.
* Chat do dia mantido em memória (uma consulta a menos por pergunta).
* A pergunta é gravada no banco **em paralelo** com a chamada ao Gemini.
* `thinkingLevel: "low"` nas conversas e `"minimal"` na transcrição.

Detalhes em [HARPIA.md](HARPIA.md).

## 7. Voz

* Transcrição em uma chamada, com áudio inline, `temperature 0` e limite de tokens de saída.
* Só o início da resposta é falado (até 3 linhas / 240 caracteres) — menos áudio para gerar.
* O chat é atualizado **ao mesmo tempo** em que a voz é gerada.

## 8. Cliente desktop

* Telas que carregam várias listas (Produtos, Etiquetas) fazem as requisições **em paralelo**.
* O cabeçalho de autenticação só é alterado quando o token muda.
* Um indicador **"⏳ Carregando..."** aparece no topo se alguma requisição passar de 300 ms — o usuário sabe que o sistema está trabalhando.

## 9. Servidor com 1 GB

* SerialGC e limite de 45% da RAM para o Java, evitando uso de swap.
* Backend parado durante a compilação no deploy.

---

# Resultados em Produção

Medições na VM Oracle (São Paulo) com o banco em São Paulo:

## Telas e operações

| Requisição | Tempo |
| --- | --- |
| `GET /setup/status` | 16 ms |
| `GET /insights/atual` | 14 ms |
| `GET /caixa` | 23–35 ms |
| `GET /produto/listar` | 32 ms |
| `GET /vendas/hoje` | 33 ms |
| `GET /etiqueta/vencendo` | 70 ms |
| `GET /estoque/listar` | 82–186 ms |
| `GET /cozinha/estoque` | 105 ms |
| `GET /chat/historico/{data}` | 117 ms |
| `POST /auth/login` | 179 ms |

Ao abrir o sistema, o login e as 10 consultas da tela principal terminam em **menos de 1 segundo**.

## HarpIA

| Etapa | Antes | Depois | Redução |
| --- | --- | --- | --- |
| Transcrever áudio | 5,7 s | 3,4 s | −40% |
| Perguntar (com ferramenta) | 5,5 s | 3,7 s | −33% |
| Gerar voz | 6,1 s | 4,1 s | −33% |
| **Ciclo completo por voz** | **17,3 s** | **11,2 s** | **−35%** |

O tempo restante da HarpIA é praticamente todo processamento do próprio Gemini; a parte do GastroSynq (banco, regras, formatação) ficou na casa de dezenas de milissegundos.

---

# Lições Aprendidas

* **Medir primeiro.** O maior gargalo (distância até o banco) não aparecia no código — só nos números.
* **Latência de rede domina** em sistemas com banco gerenciado: colocar servidor e banco na mesma região valeu mais do que qualquer ajuste de código.
* **Em IA, a chamada mais rápida é a que não é feita.** Formatar localmente o que não precisa de linguagem natural economiza segundos por pergunta.
* **Pequenos servidores exigem configuração consciente** de memória, conexões e logs.

---

# Próximas Otimizações Possíveis

* Enviar o áudio direto na pergunta, eliminando a etapa de transcrição separada (uma ida ao Gemini a menos).
* Reproduzir a voz em streaming, começando a falar antes do áudio completo.
* Desativar `spring.jpa.open-in-view`, encerrando a sessão do banco mais cedo em cada requisição.