# DOCUMENTATION.md

# GastroSynq — Documentação Técnica

## 1. Visão Geral

O GastroSynq é um sistema ERP voltado para operações de restaurantes, desenvolvido utilizando arquitetura cliente-servidor.

O objetivo principal da solução é centralizar processos operacionais e administrativos em uma única plataforma, reduzindo erros operacionais e aumentando a rastreabilidade das informações.

A aplicação foi construída utilizando:

* Frontend Desktop em C#
* Backend REST em Java Spring Boot
* Banco PostgreSQL
* Autenticação JWT
* Controle de acesso por perfis

---

# 2. Objetivos

## Objetivo Principal

Disponibilizar uma plataforma integrada para gerenciamento operacional de restaurantes.

## Objetivos Específicos

* Centralizar operações de venda
* Controlar estoque e validade
* Gerenciar funcionários
* Registrar movimentações financeiras
* Permitir rastreabilidade de produtos
* Automatizar geração de etiquetas
* Reduzir processos manuais

---

# 3. Escopo do Sistema

O GastroSynq contempla os seguintes domínios:

### Segurança

* Login
* Autenticação JWT
* Controle de acesso

### Recursos Humanos

* Funcionários
* Perfis de acesso

### Estoque

* Produtos
* Controle de estoque
* Controle de lotes
* Controle de validade

### Operação

* PDV
* Carrinho de vendas
* Pagamentos

### Financeiro

* Abertura de caixa
* Fechamento de caixa
* Histórico financeiro

### Relatórios

* Histórico de vendas
* Detalhamento de vendas
* Histórico de caixas

---

# 4. Requisitos Funcionais

## RF-01 — Autenticação

O sistema deve permitir autenticação através de login e senha.

---

## RF-02 — Controle de Permissões

O sistema deve restringir funcionalidades conforme o perfil do usuário.

---

## RF-03 — Cadastro de Funcionários

O sistema deve permitir:

* Inserção
* Atualização
* Consulta
* Controle de status

---

## RF-04 — Cadastro de Produtos

O sistema deve permitir:

* Cadastro
* Alteração
* Consulta
* Controle de disponibilidade

---

## RF-05 — Controle de Estoque

O sistema deve registrar:

* Entradas
* Saídas
* Quantidade disponível

---

## RF-06 — Controle de Lotes

O sistema deve permitir associar produtos a lotes específicos.

---

## RF-07 — Controle de Validade

O sistema deve armazenar e consultar datas de validade.

---

## RF-08 — Geração de Etiquetas

O sistema deve gerar etiquetas contendo:

* Produto
* Lote
* Validade
* Responsável

---

## RF-09 — Operação de PDV

O sistema deve permitir:

* Pesquisa de produtos
* Adição ao carrinho
* Cálculo automático
* Finalização de venda

---

## RF-10 — Registro de Pagamentos

O sistema deve registrar pagamentos vinculados às vendas.

---

## RF-11 — Controle de Caixa

O sistema deve permitir:

* Abrir caixa
* Fechar caixa
* Consultar histórico

---

## RF-12 — Relatórios

O sistema deve permitir consulta de:

* Vendas
* Caixas
* Detalhes operacionais

---

# 5. Requisitos Não Funcionais

## RNF-01 — Segurança

Utilização de autenticação JWT.

---

## RNF-02 — Integridade

Operações financeiras devem manter consistência transacional.

---

## RNF-03 — Disponibilidade

A aplicação deve operar enquanto houver comunicação com a API.

---

## RNF-04 — Escalabilidade

A arquitetura deve permitir evolução futura para clientes Web e Mobile.

---

## RNF-05 — Manutenibilidade

As responsabilidades devem estar separadas por camadas.

---

## RNF-06 — Usabilidade

A interface deve permitir execução rápida das operações do PDV.

---

# 6. Casos de Uso

## UC-01 — Realizar Login

### Ator

Funcionário

### Fluxo

1. Informa e-mail
2. Informa senha
3. Sistema valida credenciais
4. Sistema gera JWT
5. Usuário acessa funcionalidades autorizadas

---

## UC-02 — Abrir Caixa

### Ator

Operador

### Fluxo

1. Informa valor inicial
2. Sistema cria registro
3. Caixa passa para estado aberto

---

## UC-03 — Registrar Venda

### Ator

Operador

### Fluxo

1. Seleciona produtos
2. Define quantidades
3. Sistema calcula total
4. Informa forma de pagamento
5. Sistema registra venda
6. Sistema registra pagamento

---

## UC-04 — Fechar Caixa

### Ator

Operador

### Fluxo

1. Solicita fechamento
2. Sistema calcula resumo
3. Sistema registra valor final
4. Caixa passa para estado fechado

---

## UC-05 — Emitir Etiqueta

### Ator

Operador

### Fluxo

1. Seleciona produto
2. Seleciona lote
3. Sistema gera etiqueta
4. Impressão é enviada para impressora

---

# 7. Regras de Negócio

## RN-01

Não é permitido realizar vendas sem caixa aberto.

---

## RN-02

Toda venda deve possuir pelo menos um item.

---

## RN-03

Todo pagamento deve estar associado a uma venda.

---

## RN-04

Todo lote deve possuir validade.

---

## RN-05

Somente usuários autenticados podem acessar funcionalidades protegidas.

---

## RN-06

Produtos utilizados em vendas devem existir previamente cadastrados.

---

## RN-07

O fechamento de caixa deve registrar os valores finais da operação.

---

# 8. Fluxo Operacional

## Fluxo de Venda

```text
Login
 ↓
Abrir Caixa
 ↓
Selecionar Produtos
 ↓
Carrinho
 ↓
Pagamento
 ↓
Registro da Venda
 ↓
Histórico
```

---

## Fluxo de Estoque

```text
Cadastro
 ↓
Entrada
 ↓
Controle de Lote
 ↓
Controle de Validade
 ↓
Movimentação
```

---

# 9. Componentes do Sistema

## Desktop Client

Responsável por:

* Interface gráfica
* Experiência do usuário
* Consumo da API

Tecnologias:

* C#
* WinForms
* Krypton Toolkit

---

## API Backend

Responsável por:

* Regras de negócio
* Segurança
* Persistência

Tecnologias:

* Spring Boot
* Spring Security
* JWT
* Hibernate

---

## Banco de Dados

Responsável por:

* Persistência
* Integridade
* Relacionamentos

Tecnologia:

* PostgreSQL

---

# 10. Integrações

## Impressora Niimbot B1

Integração utilizada para:

* Geração de etiquetas
* Identificação de produtos
* Controle de lote
* Controle de validade

---

## API REST

Comunicação entre cliente desktop e backend.

Protocolo:

HTTP/HTTPS

Formato:

JSON

---

# 11. Segurança

## Autenticação

JWT (JSON Web Token)

---

## Controle de Acesso

RBAC (Role-Based Access Control)

Perfis:

* Administrador
* Operador
* Funcionário

---

## Proteção de Dados

* Senhas criptografadas
* Endpoints protegidos
* Validação de autenticação

---

# 12. Tratamento de Erros

O sistema implementa tratamento para:

* Credenciais inválidas
* Recursos inexistentes
* Operações inválidas
* Erros de validação
* Erros de integração

Mensagens são retornadas de forma controlada para evitar exposição indevida de detalhes internos.

---

# 13. Estratégias de Validação

## Backend

Validações realizadas na camada de serviço:

* Existência de entidades
* Consistência de relacionamentos
* Integridade transacional

---

## Frontend

Validações realizadas antes do envio:

* Campos obrigatórios
* Formatos
* Seleções obrigatórias

---

# 14. Considerações Técnicas

O GastroSynq foi projetado utilizando princípios de separação de responsabilidades e arquitetura em camadas.

A divisão entre Desktop, API e Banco de Dados reduz acoplamento, facilita manutenção e possibilita evolução futura para novos clientes consumidores da API sem necessidade de alteração das regras de negócio centrais.
