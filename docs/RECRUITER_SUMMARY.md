# RECRUITER_SUMMARY.md

# GastroSynq — Executive Summary

## Resumo Executivo

O GastroSynq é um sistema de gestão para restaurantes **em produção**, com frontend desktop em C# WinForms, backend REST em Java Spring Boot rodando em Docker na Oracle Cloud, banco PostgreSQL no Supabase e uma **assistente de IA (HarpIA)** construída sobre o Google Gemini.

O diferencial do projeto é que a IA **opera o sistema de verdade** — registra vendas, movimenta estoque, gera etiquetas, abre e fecha caixa — por texto ou por voz, **sempre com confirmação humana por PIN** e passando pelas mesmas regras de negócio e permissões das telas.

Embora o código-fonte não esteja disponível publicamente por razões comerciais, este repositório documenta as decisões arquiteturais, os desafios técnicos e as soluções implementadas.

---

# Em Números

| Item | Valor |
| --- | --- |
| Tabelas no banco | 28 |
| Operações disponíveis para a IA | 40+ (10 módulos) |
| Requisitos funcionais | 25 |
| Regras de negócio documentadas | 17 |
| Consultas das telas em produção | 15–190 ms |
| Ciclo completo de voz da IA | 17,3 s → 11,2 s após otimização |
| Infraestrutura | VM de 1 GB, custo praticamente zero |

---

# Escopo do Projeto

O sistema resolve problemas comuns em operações gastronômicas:

* Controle manual de estoque e desperdício sem registro
* Controle inadequado de validade
* Falta de rastreabilidade de produtos e ações
* Erros em vendas, troco e fechamento de caixa
* Processos manuais de etiquetagem
* Baixa integração entre estoque, cozinha e caixa
* Equipe sem tempo para navegar em telas durante o movimento

---

# Principais Tecnologias Utilizadas

| Área | Tecnologias |
| --- | --- |
| Frontend | C# 7.3, .NET Framework 4.8, Windows Forms, Krypton Toolkit, NAudio |
| Backend | Java 21, Spring Boot 3, Spring Security, JWT, JPA/Hibernate, WebSocket |
| Banco de Dados | PostgreSQL (Supabase) |
| Inteligência Artificial | Google Gemini — function calling, transcrição de áudio, síntese de voz, Google Search, saída estruturada |
| Infraestrutura | Docker, Oracle Cloud (Ubuntu), HikariCP |
| Integrações | Impressoras Niimbot e Windows (térmicas), APIs REST |

---

# Principais Problemas Resolvidos

## IA que executa operações com segurança

* Function calling: a IA escolhe operações existentes da API, sem acesso direto ao banco.
* Toda alteração gera um pop-up com resumo e só é executada com o **PIN** de um funcionário.
* O **cargo do dono do PIN** decide se a ação é permitida — como a senha do gerente num PDV.
* A IA nunca recebe IDs internos; itens são localizados por nome com busca tolerante a erros de digitação.

## Voz em qualquer tela

* Bolinha flutuante grava o pedido, transcreve, executa e responde falando.
* Tratamento do áudio gerado para eliminar ruídos.

## Resiliência e custo da IA

* Rotação de até 30 chaves de API com "castigo" automático para chaves no limite.
* Orçamento máximo de 60 s por pergunta, com mensagem amigável em caso de falha.
* Modelo econômico (flash-lite) e redução de chamadas desnecessárias.

## Controle de Estoque e Cozinha

* Entradas com lote, validade e custo.
* Saída **somente para a cozinha ou como perda** — nenhum produto some sem justificativa.
* Estoque próprio da cozinha, com uso e perda.

## Validade e Rastreabilidade

* Validade calculada por estado do produto.
* Etiquetas com responsável e alerta de vencimento.
* Toda movimentação, venda, etiqueta e ação da IA registra quem fez e quando.

## PDV e Caixa

* Carrinho, troco, pagamentos em dinheiro/Pix/cartão.
* Valor esperado na gaveta e fechamento automático de caixa esquecido.

## Desempenho em infraestrutura mínima

* Otimização guiada por medições reais de produção.
* Banco migrado para a mesma região do servidor, eliminação de consultas N+1, pool de conexões ajustado, cache de autenticação.

---

# Competências Demonstradas

## Inteligência Artificial Aplicada

* Integração com LLM via function calling
* Design de prompts de sistema (persona, regras de decisão)
* Human-in-the-loop para ações críticas
* Processamento de voz (STT e TTS)
* Saída estruturada com JSON schema
* Controle de custo, latência e cota

## Arquitetura de Software

* Arquitetura cliente-servidor em camadas
* Reutilização de regras de negócio por múltiplos clientes (telas e IA)
* Modularização por domínio

## Backend Engineering

* APIs REST com Spring Boot
* Segurança com JWT, RBAC e PIN
* Transações e consistência de dados
* Tarefas agendadas
* Tratamento global de erros

## Banco de Dados

* Modelagem relacional de 28 tabelas
* Rastreabilidade e exclusão lógica
* Migração de banco entre regiões (`pg_dump`/`psql`)
* Diagnóstico de problemas de conexão e consultas N+1

## Infraestrutura e DevOps

* Docker e Docker Compose
* Implantação em nuvem (Oracle Cloud)
* Script de deploy automatizado
* Ajuste de JVM para servidor de 1 GB
* Gestão de segredos por variáveis de ambiente
* Monitoramento de tempo de requisições

## Desktop Development

* WinForms com layouts adaptáveis
* Consumo assíncrono de APIs
* Gravação e reprodução de áudio
* Integração com impressoras

## Engenharia de Software

* Versionamento com Git
* Documentação técnica
* Validações de negócio
* Otimização guiada por métricas

---

# Desafios Técnicos Relevantes

## Fazer a IA agir sem abrir brechas

A IA precisava executar operações reais sem poder burlar regras. A solução foi fazê-la chamar a **própria API** do sistema (mesmas validações e transações) e exigir **confirmação humana com PIN**, verificando o cargo de quem confirma.

## Latência

Cada chamada ao modelo de IA leva segundos. Pedidos de lista, alterações e erros passaram a dispensar a segunda chamada ao modelo, e o banco foi migrado para a mesma região do servidor.

## Servidor de 1 GB

Compilação e aplicação não cabiam juntas na memória; o deploy foi redesenhado e a JVM ajustada (SerialGC, limite de memória).

## Limite de conexões do banco

O pooler do Supabase aceita 15 conexões; conexões abandonadas impediam o backend de subir. Diagnóstico pelo `pg_stat_activity` e pool fixo de 4 conexões.

## Qualidade do áudio

A voz gerada terminava com um chiado. A causa era um cabeçalho WAV duplicado; a solução foi extrair apenas os dados de áudio e aplicar fade-out.

---

# Complexidade do Projeto

O GastroSynq não é um CRUD simples. Ele integra:

* Segurança (JWT, cargos, PIN)
* Estoque principal e cozinha
* Validade e etiquetagem
* Vendas e financeiro
* Rastreabilidade e auditoria
* Inteligência artificial com voz
* Infraestrutura em nuvem

---

# Impacto Profissional

O desenvolvimento do GastroSynq proporcionou experiência prática em:

* Construção de um sistema completo, do banco à nuvem
* Aplicação responsável de IA generativa em um negócio real
* Levantamento de requisitos e modelagem de domínio
* Diagnóstico e otimização de desempenho em produção
* Trabalho em equipe com versionamento

---

# Documentação Completa

* [README](../README.md)
* [Requisitos e regras de negócio](DOCUMENTATION.md)
* [Arquitetura](ARCHITECTURE.md)
* [Banco de dados](DATABASE.md)
* [HarpIA](HARPIA.md)
* [Infraestrutura](INFRAESTRUTURA.md)
* [Desempenho](DESEMPENHO.md)

---

# Observação

Por questões comerciais e de propriedade intelectual, o código-fonte não é disponibilizado publicamente.

Este repositório apresenta a arquitetura, as decisões técnicas e as competências desenvolvidas durante a construção da solução.