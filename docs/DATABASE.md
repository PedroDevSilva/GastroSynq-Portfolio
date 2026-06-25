# DATABASE.md

# Banco de Dados — GastroSynq

## Visão Geral

O GastroSynq utiliza PostgreSQL como sistema gerenciador de banco de dados relacional.

O modelo foi projetado para atender operações de restaurantes que exigem:

* Controle de estoque
* Controle de validade
* Controle de lotes
* Rastreabilidade de produtos
* Operação de PDV
* Controle financeiro de caixa
* Gestão de funcionários
* Controle de permissões
* Histórico operacional

A modelagem busca garantir integridade referencial, rastreabilidade completa e consistência transacional.

---

# Estratégia de Modelagem

O banco foi dividido em domínios funcionais:

* Segurança e usuários
* Cadastro
* Estoque
* Produção e rastreabilidade
* Vendas
* Financeiro

Essa separação reduz acoplamento e facilita futuras expansões.

---

# Domínio de Segurança

## Role

Responsável pelo controle de permissões.

### Campos principais

* id
* nome
* nivel_permissao

### Responsabilidade

Define o conjunto de permissões associado aos usuários do sistema.

---

## Funcionario

Representa usuários autenticáveis.

### Campos principais

* id
* nome
* email
* cpf
* senha
* ativo
* role_id

### Relacionamentos

* N:1 Role
* 1:N Venda
* 1:N CaixaAberto
* 1:N Etiqueta
* 1:N MovimentacaoEstoque

### Responsabilidade

Controlar autenticação, autorização e rastreabilidade das ações realizadas.

---

# Domínio Comercial

## ProdutoVenda

Catálogo simplificado utilizado pelo PDV.

### Campos principais

* id
* nome
* preco
* ativo

### Responsabilidade

Representar produtos disponíveis para comercialização.

---

## Venda

Registro principal das operações comerciais.

### Campos principais

* id
* data_venda
* valor_total
* funcionario_id
* caixa_id

### Relacionamentos

* N:1 Funcionario
* N:1 CaixaAberto
* 1:N ItemVenda
* 1:1 Pagamento

### Responsabilidade

Centralizar a transação de venda.

---

## ItemVenda

Itens pertencentes a uma venda.

### Campos principais

* quantidade
* preco_unitario
* subtotal

### Relacionamentos

* N:1 Venda
* N:1 ProdutoVenda

### Responsabilidade

Registrar os produtos efetivamente vendidos.

---

## Pagamento

Registro financeiro associado à venda.

### Campos principais

* metodo
* valor
* status
* nota_fiscal
* transacao_id

### Relacionamentos

* N:1 Venda
* N:1 Funcionario

### Responsabilidade

Controlar o recebimento financeiro da operação.

---

# Domínio Financeiro

## CaixaAberto

Representa sessões operacionais de caixa.

### Campos principais

* data_abertura
* data_fechamento
* valor_inicial
* valor_final
* aberto

### Relacionamentos

* N:1 Funcionario
* 1:N Venda

### Responsabilidade

Garantir auditoria financeira diária.

---

# Domínio de Cadastro

## Fornecedor

Cadastro de fornecedores.

### Campos principais

* nome
* cnpj
* telefone
* email

### Relacionamentos

* 1:N Produto

### Responsabilidade

Gerenciar origem dos insumos e mercadorias.

---

## Produto

Representa itens controlados em estoque.

### Campos principais

* nome
* descricao
* preco
* controla_lote
* perecivel
* estoque_minimo

### Relacionamentos

* N:1 Fornecedor
* 1:N Estoque
* 1:N LoteEstoque
* 1:N MovimentacaoEstoque
* 1:N Etiqueta

### Responsabilidade

Centralizar informações operacionais dos produtos.

---

## Tag

Etiquetas de categorização.

### Relacionamentos

* N:1 Produto

### Responsabilidade

Facilitar classificação e busca.

---

# Domínio de Estoque

## Estoque

Representa posição atual do estoque.

### Campos principais

* quantidade
* minimo_alertas
* data_atualizacao

### Relacionamentos

* N:1 Produto
* N:1 EstadoProduto

### Responsabilidade

Controlar disponibilidade atual dos produtos.

---

## LoteEstoque

Controle individualizado por lote.

### Campos principais

* id_lote
* data_entrada
* data_validade
* qtd_atual

### Relacionamentos

* N:1 Produto

### Responsabilidade

Permitir rastreamento de origem e validade.

---

## MovimentacaoEstoque

Histórico de entradas e saídas.

### Campos principais

* tipo
* quantidade
* observacao
* data_movimentacao

### Relacionamentos

* N:1 Produto
* N:1 LoteEstoque
* N:1 Funcionario

### Responsabilidade

Registrar toda alteração realizada no estoque.

---

# Domínio de Qualidade e Rastreabilidade

## EstadoProduto

Estados operacionais do produto.

### Exemplos

* Cru
* Preparado
* Resfriado
* Congelado
* Descartado

### Responsabilidade

Padronizar o ciclo de vida operacional.

---

## RegraValidade

Configuração de validade por estado.

### Campos principais

* dias_validade
* ativo

### Relacionamentos

* N:1 EstadoProduto

### Responsabilidade

Automatizar cálculo de validade.

---

## ProdutoEstadoHistorico

Histórico de mudanças de estado.

### Campos principais

* data_inicio
* data_fim
* origem

### Relacionamentos

* N:1 Produto
* N:1 EstadoProduto
* N:1 Funcionario

### Responsabilidade

Garantir rastreabilidade completa.

---

## Etiqueta

Representação física do produto identificado.

### Campos principais

* lote
* data_geracao
* validade_calculada

### Relacionamentos

* N:1 Produto
* N:1 Funcionario
* N:1 LoteEstoque

### Responsabilidade

Identificar produtos fisicamente.

---

## EtiquetaGeracao

Registro de geração das etiquetas.

### Responsabilidade

Auditoria de criação.

---

## ImpressaoEtiqueta

Controle de impressão.

### Campos principais

* status
* tentativas
* log_erro

### Responsabilidade

Monitorar integração com impressoras.

---

## LogRastreabilidade

Registro de auditoria operacional.

### Campos principais

* acao
* descricao
* data

### Responsabilidade

Garantir histórico completo das operações.

---

# Fluxo Transacional

## Venda

1. Caixa aberto
2. Produtos adicionados ao carrinho
3. Venda criada
4. Itens registrados
5. Pagamento registrado
6. Estoque atualizado
7. Auditoria registrada

---

## Controle de Lotes

1. Entrada de lote
2. Associação ao produto
3. Controle de validade
4. Movimentações registradas
5. Consumo rastreável

---

## Impressão de Etiquetas

1. Produto selecionado
2. Validade calculada
3. Etiqueta criada
4. Registro de impressão
5. Log armazenado

---

# Estratégias de Integridade

* Chaves primárias em todas as entidades
* Foreign Keys para relacionamentos críticos
* Restrições de unicidade em CPF, Email e CNPJ
* Controle transacional via Spring Data JPA
* Auditoria de operações críticas

---

# Estratégias de Indexação

Índices recomendados:

* funcionario(email)
* funcionario(cpf)
* produto(nome)
* lote_estoque(id_lote)
* venda(data_venda)
* pagamento(data_pagamento)
* caixa_aberto(aberto)
* movimentacao_estoque(data_movimentacao)

---

# Estratégias de Escalabilidade

Possíveis evoluções futuras:

* Particionamento de vendas por período
* Particionamento de logs de rastreabilidade
* Cache para consultas frequentes
* Read Replicas PostgreSQL
* Eventos assíncronos para auditoria
* Integração com mensageria (RabbitMQ/Kafka)

---

# Considerações Finais

O modelo foi desenvolvido para atender cenários reais de operação gastronômica, priorizando integridade, rastreabilidade, auditabilidade e manutenção de longo prazo.

A estrutura suporta expansão para múltiplas unidades, integração com sistemas externos e crescimento gradual da plataforma.
