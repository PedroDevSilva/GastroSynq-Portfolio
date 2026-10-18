# DOCUMENTATION.md

# GastroSynq — Documentação Técnica

## 1. Visão Geral

O GastroSynq é um sistema de gestão (ERP) para operações de restaurantes, desenvolvido com arquitetura cliente-servidor e com uma assistente de inteligência artificial integrada, a **HarpIA**.

O objetivo principal da solução é centralizar processos operacionais e administrativos em uma única plataforma, reduzindo erros operacionais, aumentando a rastreabilidade das informações e permitindo que a equipe opere o sistema também por linguagem natural — texto ou voz.

A aplicação foi construída utilizando:

* Frontend Desktop em C# (WinForms)
* Backend REST em Java Spring Boot
* Banco PostgreSQL (Supabase)
* Autenticação JWT e controle de acesso por cargo
* Confirmação de ações com PIN
* IA com Google Gemini (function calling, transcrição e voz)
* Implantação em Docker na Oracle Cloud

Documentos relacionados:

* [ARCHITECTURE.md](ARCHITECTURE.md) — arquitetura
* [DATABASE.md](DATABASE.md) — banco de dados
* [HARPIA.md](HARPIA.md) — assistente de IA
* [INFRAESTRUTURA.md](INFRAESTRUTURA.md) — implantação
* [DESEMPENHO.md](DESEMPENHO.md) — otimizações e medições

---

# 2. Objetivos

## Objetivo Principal

Disponibilizar uma plataforma integrada para o gerenciamento operacional de restaurantes, com uma assistente de IA capaz de consultar e operar o sistema de forma segura.

## Objetivos Específicos

* Centralizar operações de venda e caixa
* Controlar estoque principal, estoque da cozinha, lotes e validade
* Garantir que toda saída de estoque seja justificada (cozinha ou perda)
* Gerenciar funcionários com cargos e permissões
* Automatizar a geração de etiquetas de validade
* Permitir rastreabilidade de produtos e ações
* Permitir operação por linguagem natural e por voz
* Gerar análises automáticas do negócio
* Reduzir processos manuais

---

# 3. Escopo do Sistema

### Segurança

* Primeiro acesso com criação do gerente
* Login com JWT
* Permissões por cargo
* PIN para confirmação de ações

### Recursos Humanos

* Funcionários
* Cargos (Funcionário, Gerente, Admin)

### Cadastros

* Produtos (insumos)
* Fornecedores
* Cardápio (produtos de venda)

### Estoque

* Entrada com lote, validade e custo
* Perda
* Envio para a cozinha
* Estoque mínimo
* Histórico de movimentações

### Cozinha

* Estoque da cozinha
* Registro de uso
* Registro de perda

### Qualidade

* Etiquetas de validade
* Regras de validade por estado do produto
* Alerta de vencimento

### Operação

* PDV
* Carrinho de vendas
* Pagamentos (dinheiro, Pix, cartão)

### Financeiro

* Abertura e fechamento de caixa
* Fechamento automático
* Histórico de caixas

### Relatórios e Análises

* Relatório de vendas por período
* Detalhamento de vendas
* Dashboards
* Insights automáticos da HarpIA

### Inteligência Artificial

* Chat com a HarpIA
* Comando de voz (botão Gravar e bolinha flutuante)
* Resposta falada
* Execução de operações com confirmação
* Pesquisa de mercado

---

# 4. Requisitos Funcionais

## RF-01 — Autenticação

O sistema deve permitir autenticação por login e senha, bloqueando funcionários inativos.

---

## RF-02 — Controle de Permissões

O sistema deve restringir funcionalidades conforme o cargo do usuário:

| Cargo | Acesso |
| --- | --- |
| Funcionário | PDV, caixa, etiquetas, cozinha, HarpIA e consultas |
| Gerente | Tudo do funcionário + estoque, cadastros, mínimos, PINs e exclusões |
| Admin | Tudo |

---

## RF-03 — Primeiro Acesso

Em uma instalação sem usuários, o sistema deve exibir uma tela de configuração inicial que cria o primeiro **gerente**.

---

## RF-04 — Cadastro de Funcionários

O sistema deve permitir inserir, atualizar, consultar e desativar funcionários, e definir o PIN de cada um.

---

## RF-05 — Cadastro de Produtos

O sistema deve permitir cadastrar, alterar, consultar e desativar produtos de estoque, com fornecedor opcional.

---

## RF-06 — Cadastro de Fornecedores

O sistema deve permitir cadastrar, alterar, consultar e remover fornecedores, inclusive a partir da tela de produto.

---

## RF-07 — Cardápio

O sistema deve permitir cadastrar itens de venda, alterar preço e desativar itens.

---

## RF-08 — Entrada de Estoque

O sistema deve registrar entradas com quantidade, lote, validade e custo unitário (opcional), registrando o funcionário responsável.

---

## RF-09 — Saída de Estoque

O sistema deve permitir a saída de produtos do estoque principal somente:

* para a **cozinha**; ou
* como **perda**.

---

## RF-10 — Estoque da Cozinha

O sistema deve manter o saldo da cozinha e permitir registrar **uso** e **perda**.

---

## RF-11 — Estoque Mínimo

O sistema deve permitir definir o estoque mínimo (principal e cozinha) e listar produtos abaixo dele.

---

## RF-12 — Histórico de Movimentações

O sistema deve registrar e exibir todas as movimentações, com data, tipo, quantidade e responsável.

---

## RF-13 — Geração de Etiquetas

O sistema deve gerar etiquetas contendo produto, lote, validade calculada, data de geração e responsável, e permitir reimpressão.

---

## RF-14 — Alerta de Validade

O sistema deve alertar, ao abrir, sobre etiquetas próximas do vencimento.

---

## RF-15 — Operação de PDV

O sistema deve permitir pesquisar itens do cardápio, montar o carrinho, calcular o total e o troco, e finalizar a venda.

---

## RF-16 — Registro de Pagamentos

O sistema deve registrar o pagamento vinculado à venda (dinheiro, Pix ou cartão).

---

## RF-17 — Controle de Caixa

O sistema deve permitir abrir e fechar o caixa, calcular o valor esperado na gaveta e consultar o histórico (mais recente primeiro).

---

## RF-18 — Fechamento Automático de Caixa

O sistema deve fechar automaticamente, às 05:00, um caixa esquecido aberto.

---

## RF-19 — Relatórios

O sistema deve permitir consulta de vendas por período, detalhes de cada venda e histórico de caixas.

---

## RF-20 — Assistente de IA (HarpIA)

O sistema deve permitir que o usuário consulte informações e solicite operações em linguagem natural, por meio de um chat.

---

## RF-21 — Confirmação de Ações da IA

Toda alteração solicitada à HarpIA deve ser exibida em um pop-up de confirmação e só ser executada após a digitação do PIN de um funcionário com cargo suficiente.

---

## RF-22 — Comando de Voz

O sistema deve permitir perguntar à HarpIA por voz, pelo botão "Gravar" do chat e por uma bolinha flutuante disponível em qualquer tela, com resposta falada.

---

## RF-23 — Histórico de Conversas

O sistema deve salvar as conversas com a HarpIA por dia e permitir consultar dias anteriores.

---

## RF-24 — Insights Automáticos

O sistema deve gerar periodicamente análises sobre estoque, etiquetas e vendas, exibidas nos dashboards.

---

## RF-25 — Pesquisa de Mercado

A HarpIA deve permitir consultas sobre informações externas (preços de insumos, tendências), indicando que a informação vem de fora do sistema.

---

# 5. Requisitos Não Funcionais

## RNF-01 — Segurança

Autenticação JWT, senhas e PINs com hash, permissões por cargo e segredos fora do código.

---

## RNF-02 — Integridade

Operações que alteram mais de uma tabela (venda, entrada, envio para a cozinha, etiqueta, fechamento de caixa) devem ser transacionais.

---

## RNF-03 — Rastreabilidade

Toda movimentação, venda, etiqueta e ação da IA deve registrar o funcionário responsável e a data/hora.

---

## RNF-04 — Desempenho

Consultas das telas devem responder em menos de 200 ms em produção; a HarpIA deve evitar chamadas desnecessárias ao modelo de IA.

---

## RNF-05 — Disponibilidade

O backend deve reiniciar automaticamente em caso de falha; a IA deve continuar funcionando se uma chave de API atingir o limite de uso (rotação de chaves).

---

## RNF-06 — Tempo Limite

Nenhuma chamada à IA deve deixar o usuário esperando mais de 60 s; em caso de falha, uma mensagem clara deve ser exibida.

---

## RNF-07 — Escalabilidade

A arquitetura deve permitir evolução futura para clientes Web e Mobile, consumindo a mesma API.

---

## RNF-08 — Manutenibilidade

As responsabilidades devem estar separadas por camadas; modelos de IA e horários de tarefas devem ser configuráveis sem recompilar.

---

## RNF-09 — Usabilidade

A interface deve permitir execução rápida das operações do PDV, mostrar indicador de carregamento e exibir mensagens de erro compreensíveis.

---

## RNF-10 — Custo

A solução deve operar em infraestrutura de baixo custo (camada gratuita da Oracle Cloud e do Supabase, modelo de IA econômico).

---

# 6. Casos de Uso

## UC-01 — Primeiro Acesso

### Ator

Responsável pelo restaurante

### Fluxo

1. Abre o sistema pela primeira vez
2. Sistema detecta que não há usuários
3. Informa os dados do gerente
4. Sistema cria o gerente
5. Gerente faz login e cadastra a equipe

---

## UC-02 — Realizar Login

### Ator

Funcionário

### Fluxo

1. Informa e-mail
2. Informa senha
3. Sistema valida credenciais e se o funcionário está ativo
4. Sistema gera JWT
5. Sistema exibe alerta de etiquetas vencendo, se houver
6. Usuário acessa as funcionalidades do seu cargo

---

## UC-03 — Abrir Caixa

### Ator

Operador

### Fluxo

1. Informa valor inicial
2. Sistema cria registro
3. Caixa passa para estado aberto

---

## UC-04 — Registrar Venda

### Ator

Operador

### Fluxo

1. Seleciona itens do cardápio
2. Define quantidades
3. Sistema calcula total
4. Informa forma de pagamento (e valor recebido, se dinheiro)
5. Sistema calcula o troco
6. Sistema registra venda e pagamento

### Exceção

* Valor recebido em dinheiro menor que o total: sistema mostra "Faltam R$ X" e não permite finalizar.

---

## UC-05 — Fechar Caixa

### Ator

Operador

### Fluxo

1. Solicita fechamento
2. Sistema calcula resumo (dinheiro, Pix, cartão e valor esperado na gaveta)
3. Operador informa valor contado
4. Caixa passa para estado fechado

---

## UC-06 — Registrar Entrada de Estoque

### Ator

Gerente

### Fluxo

1. Seleciona produto
2. Informa quantidade, validade e preço pago (opcional)
3. Sistema cria o lote e atualiza o saldo
4. Se informado, o preço do produto é atualizado
5. Movimentação registrada com o responsável

---

## UC-07 — Enviar Produto para a Cozinha

### Ator

Funcionário

### Fluxo

1. Seleciona produto e quantidade
2. Sistema verifica saldo do estoque principal
3. Sistema baixa do estoque principal e adiciona à cozinha
4. Movimentações registradas nas duas pontas

---

## UC-08 — Registrar Perda

### Ator

Funcionário (cozinha) / Gerente (estoque principal)

### Fluxo

1. Seleciona produto e quantidade
2. Marca como perda e informa observação
3. Sistema baixa o saldo (no estoque principal, consumindo primeiro os lotes vencidos)
4. Movimentação `PERDA` registrada

---

## UC-09 — Emitir Etiqueta

### Ator

Funcionário

### Fluxo

1. Seleciona produto e estado
2. Sistema calcula a validade pela regra do estado
3. Sistema transfere o produto para a cozinha e registra a etiqueta
4. Impressão é enviada para a impressora configurada

---

## UC-10 — Consultar pela HarpIA

### Ator

Funcionário

### Fluxo

1. Digita ou fala a pergunta (ex.: "o que está abaixo do mínimo?")
2. HarpIA identifica a consulta e busca os dados
3. Resposta exibida no chat (e falada, se veio por voz)

---

## UC-11 — Executar Operação pela HarpIA

### Ator

Funcionário

### Fluxo

1. Solicita a operação (ex.: "vende 2 cocas e 1 misto quente no pix")
2. HarpIA identifica a operação e os dados
3. Se faltar informação, HarpIA pergunta
4. Sistema exibe pop-up com o resumo
5. Funcionário digita o PIN
6. Sistema verifica o cargo do dono do PIN
7. Operação executada pelas mesmas regras da tela
8. Resultado exibido no chat

### Exceções

* PIN inválido: operação não executada; após 5 erros, bloqueio de 2 minutos.
* Cargo insuficiente: operação recusada; é necessário o PIN de um cargo maior.

---

## UC-12 — Comando de Voz

### Ator

Funcionário

### Fluxo

1. Clica na bolinha flutuante (ou em "Gravar")
2. Fala o pedido e clica novamente para parar
3. Sistema transcreve o áudio
4. Segue o UC-10 ou UC-11
5. Sistema fala o início da resposta; respostas longas abrem o chat

---

# 7. Regras de Negócio

## RN-01

Não é permitido realizar vendas sem caixa aberto.

---

## RN-02

Toda venda deve possuir pelo menos um item, e apenas itens ativos do cardápio podem ser vendidos.

---

## RN-03

Todo pagamento deve estar associado a uma venda.

---

## RN-04

Em vendas em dinheiro, o valor recebido não pode ser menor que o total.

---

## RN-05

O valor esperado na gaveta é o valor inicial mais as vendas em dinheiro; Pix e cartão são contabilizados separadamente.

---

## RN-06

Um caixa aberto até as 05:00 é fechado automaticamente, sem valor final contado.

---

## RN-07

Um produto só sai do estoque principal indo para a **cozinha** ou como **perda**.

---

## RN-08

Perdas do estoque principal consomem primeiro os lotes vencidos.

---

## RN-09

A validade informada na entrada não pode ser uma data passada.

---

## RN-10

A etiqueta só é gerada se existir regra de validade ativa para o estado do produto.

---

## RN-11

Somente usuários autenticados e ativos podem acessar funcionalidades protegidas.

---

## RN-12

Ninguém pode cadastrar um funcionário com cargo acima do seu nem desativar a si mesmo, e sempre deve restar ao menos um gerente ativo.

---

## RN-13

Exclusões de funcionários, produtos e itens de cardápio são lógicas (desativação), preservando o histórico.

---

## RN-14

O PIN de cada funcionário é único.

---

## RN-15

Nenhuma alteração solicitada à HarpIA é executada sem confirmação com PIN; consultas não exigem confirmação.

---

## RN-16

O responsável registrado por uma ação da HarpIA é o dono do PIN que a confirmou, não o usuário logado no computador.

---

## RN-17

Telefone e CNPJ de fornecedores são armazenados apenas com dígitos.

---

# 8. Fluxo Operacional

## Fluxo de Venda

```text
Login
 ↓
Abrir Caixa
 ↓
Selecionar Itens (tela ou HarpIA)
 ↓
Carrinho
 ↓
Pagamento (troco calculado)
 ↓
Registro da Venda
 ↓
Fechamento do Caixa
 ↓
Histórico / Relatórios
```

---

## Fluxo de Estoque

```text
Cadastro do Produto
 ↓
Entrada (lote, validade, custo)
 ↓
Estoque Principal
 ├─→ Cozinha (com ou sem etiqueta) ─→ Uso / Perda
 └─→ Perda
 ↓
Histórico de Movimentações
```

---

## Fluxo da HarpIA

```text
Pergunta (texto ou voz)
 ↓
Consulta? ─── sim ──→ Resposta
 │
 não (alteração)
 ↓
Pop-up de confirmação
 ↓
PIN + verificação de cargo
 ↓
Execução pelas regras normais
 ↓
Resultado
```

---

# 9. Componentes do Sistema

## Desktop Client

Responsável por:

* Interface gráfica e experiência do usuário
* Consumo da API
* Gravação e reprodução de voz
* Impressão de etiquetas

Tecnologias:

* C# / .NET Framework 4.8
* WinForms
* Krypton Toolkit
* NAudio

---

## API Backend

Responsável por:

* Regras de negócio
* Segurança
* Persistência
* Orquestração da IA
* Tarefas agendadas

Tecnologias:

* Java 21 / Spring Boot 3
* Spring Security + JWT
* Spring Data JPA / Hibernate
* Docker

---

## Banco de Dados

Responsável por:

* Persistência
* Integridade
* Relacionamentos

Tecnologia:

* PostgreSQL (Supabase)

---

## Assistente de IA

Responsável por:

* Interpretação de linguagem natural
* Seleção de operações
* Transcrição e síntese de voz
* Insights

Tecnologia:

* Google Gemini (flash-lite e TTS)

---

# 10. Integrações

## Impressoras de Etiqueta

* Impressoras Windows (incluindo térmica POS58)
* Niimbot B1

Utilizadas para etiquetas de validade com produto, lote, validade e responsável.

---

## Google Gemini

* Function calling (escolha de operações)
* Transcrição de áudio
* Síntese de voz (TTS)
* Pesquisa com Google Search
* Saída estruturada (insights)

---

## API REST

Comunicação entre cliente desktop e backend.

* Protocolo: HTTP
* Formato: JSON
* Autenticação: Bearer JWT

---

# 11. Segurança

## Autenticação

JWT (JSON Web Token), com verificação de funcionário ativo a cada requisição.

---

## Controle de Acesso

RBAC (Role-Based Access Control) por cargo:

* Funcionário
* Gerente
* Admin

---

## Confirmação por PIN

Ações da IA confirmadas com PIN de 4 dígitos, com bloqueio após tentativas erradas.

---

## Proteção de Dados

* Senhas e PINs com hash
* Endpoints protegidos
* Segredos em variáveis de ambiente, fora do Git

---

# 12. Tratamento de Erros

O sistema implementa tratamento para:

* Credenciais inválidas e funcionário inativo
* Permissão insuficiente
* Recursos inexistentes
* Operações inválidas (ex.: estoque insuficiente, caixa fechado)
* Erros de validação
* Indisponibilidade da IA (todas as chaves no limite ou fora do ar)

O backend devolve erros num formato padronizado (`status`, `mensagem`) por meio de um tratador global. O cliente exibe a mensagem real do backend de forma compreensível, sem expor detalhes técnicos internos.

---

# 13. Estratégias de Validação

## Backend

Validações realizadas na camada de serviço:

* Existência de entidades
* Consistência de relacionamentos
* Regras de negócio (cargos, saldo, validade, caixa aberto)
* Integridade transacional

---

## Frontend

Validações realizadas antes do envio:

* Campos obrigatórios
* Formatos
* Seleções obrigatórias
* Valor recebido no PDV

---

## IA

* O modelo nunca recebe IDs internos; itens são localizados por nome com busca tolerante a erros.
* Se faltar informação ou houver ambiguidade, a HarpIA pergunta antes de agir.
* Toda alteração passa pelas mesmas validações do backend usadas pelas telas.

---

# 14. Considerações Técnicas

O GastroSynq foi projetado com separação de responsabilidades e arquitetura em camadas.

A divisão entre Desktop, API, Banco de Dados e IA reduz acoplamento e permite que novos clientes — inclusive a própria assistente de IA — consumam as mesmas regras de negócio centrais, sem duplicação e sem brechas de segurança.