# 🍽️ GastroSynq

> Sistema ERP para Restaurantes desenvolvido utilizando arquitetura cliente-servidor, com foco em controle operacional, gestão de estoque, rastreabilidade de produtos, vendas e gestão financeira.

![Status](https://img.shields.io/badge/Status-Em%20Desenvolvimento-success)
![C#](https://img.shields.io/badge/C%23-WinForms-purple)
![Java](https://img.shields.io/badge/Java-SpringBoot-orange)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-Database-blue)
![JWT](https://img.shields.io/badge/Auth-JWT-green)
![Hibernate](https://img.shields.io/badge/ORM-Hibernate-red)

---

# Visão Geral

O GastroSynq é um sistema ERP voltado para operações de restaurantes, desenvolvido para centralizar processos operacionais e reduzir erros relacionados a vendas, estoque, rastreabilidade e controle financeiro.

O projeto foi construído utilizando uma arquitetura cliente-servidor, separando responsabilidades entre interface desktop, regras de negócio e persistência de dados.

Seu objetivo é fornecer uma plataforma integrada para:

* Controle de estoque
* Gestão de produtos
* Controle de lotes e validade
* Operação de PDV
* Gestão de caixa
* Controle de funcionários
* Emissão de etiquetas
* Histórico operacional
* Relatórios gerenciais

---

# Problemas Resolvidos

Restaurantes frequentemente enfrentam dificuldades relacionadas a:

* Controle manual de estoque
* Falta de rastreabilidade dos produtos
* Erros em vendas e pagamentos
* Falta de integração entre setores
* Ausência de histórico operacional centralizado

O GastroSynq busca resolver esses problemas através de uma plataforma única capaz de controlar toda a operação diária do estabelecimento.

---

# Público-Alvo

* Restaurantes
* Lanchonetes
* Hamburguerias
* Pizzarias
* Operações de alimentação em geral

---

# Principais Funcionalidades

## Autenticação e Segurança

* Login autenticado via JWT
* Controle de permissões por cargo
* Proteção de endpoints
* Controle de sessão

## Gestão de Funcionários

* Controle de cargos
* Controle de acesso por perfil

## Gestão de Produtos

* Cadastro de produtos
* Controle de disponibilidade
* Consulta e manutenção

## Estoque

* Controle de entradas
* Controle de saídas
* Atualização de saldo
* Histórico de movimentações

## Controle de Lotes

* Registro de lotes
* Controle de validade
* Rastreabilidade dos produtos

## Etiquetas

* Geração de etiquetas
* Impressão integrada
* Identificação de lote
* Informações de validade

## PDV

* Pesquisa de produtos
* Carrinho de vendas
* Múltiplos itens
* Pagamentos

## Controle Financeiro

* Abertura de caixa
* Fechamento de caixa
* Histórico de caixas
* Relatórios operacionais

## Relatórios

* Histórico de vendas
* Detalhamento de vendas
* Histórico financeiro

---

# Tecnologias Utilizadas

## Frontend Desktop

* C#
* WinForms
* Krypton Toolkit

## Backend

* Java
* Spring Boot
* Spring Security
* JWT
* Hibernate
* JPA

## Banco de Dados

* PostgreSQL

## Infraestrutura

* Supabase
* Git
* GitHub

## Integrações

* Impressora Niimbot B1
* Qualquer outra opção reconhecida pelo windows

---

# Arquitetura da Solução

```text
┌─────────────────────┐
│   Desktop Client    │
│  C# + WinForms      │
└──────────┬──────────┘
           │ HTTP/REST
           ▼
┌─────────────────────┐
│     Spring Boot     │
│ Business Layer      │
│ JWT Authentication  │
└──────────┬──────────┘
           │ JPA/Hibernate
           ▼
┌─────────────────────┐
│    PostgreSQL       │
│     Supabase        │
└─────────────────────┘
```

---

# Fluxo Operacional

## Venda

```text
Login
 ↓
Abertura de Caixa
 ↓
Seleção de Produtos
 ↓
Carrinho
 ↓
Pagamento
 ↓
Registro da Venda
 ↓
Atualização do Estoque
 ↓
Histórico
```

## Controle de Estoque

```text
Cadastro
 ↓
Entrada
 ↓
Lote
 ↓
Validade
 ↓
Movimentação
 ↓
Baixa
```

---

# Módulos Implementados

| Módulo              | Status |
| ------------------- | ------ |
| Login               | ✅      |
| Funcionários        | ✅      |
| Produtos            | ✅      |
| Estoque             | ✅      |
| Lotes               | ✅      |
| Validade            | ✅      |
| Etiquetas           | ✅      |
| Impressão Niimbot   | ✅      |
| PDV                 | ✅      |
| Carrinho            | ✅      |
| Pagamentos          | ✅      |
| Caixa               | ✅      |
| Relatórios          | ✅      |
| Histórico de Vendas | ✅      |

---

# Integrações

## Impressora Niimbot

Integração para emissão de etiquetas contendo:

* Produto
* Lote
* Data de geração
* Data de validade
* Responsável

---

# Estrutura Geral do Sistema

```text
Desktop Client
│
├── Login
├── Produtos
├── Estoque
├── Etiquetas
├── PDV
├── Caixa
└── Relatórios

Backend API
│
├── Controllers
├── Services
├── Repositories
├── Security
└── DTOs

Database
│
├── Funcionários
├── Produtos
├── Estoque
├── Lotes
├── Vendas
├── Pagamentos
└── Caixa
```

---

# Demonstração Visual

## Screenshots Recomendados

* Tela de Login
* Cadastro de Produtos
* Controle de Estoque
* Impressão de Etiquetas
* PDV
* Histórico de Vendas
* Fechamento de Caixa

---

# GIFs Recomendados

### Fluxo Completo de Venda

```text
Login → PDV → Pagamento → Venda Concluída
```

### Impressão de Etiquetas

```text
Produto → Etiqueta → Impressão
```

### Abertura e Fechamento de Caixa

```text
Abrir Caixa → Operar → Fechar Caixa
```

---

# Principais Aprendizados

Durante o desenvolvimento deste projeto foram aplicados conceitos relacionados a:

* Arquitetura Cliente-Servidor
* APIs REST
* JWT Authentication
* ORM com Hibernate
* Modelagem de Banco de Dados
* Controle Transacional
* Integração com Hardware
* Desenvolvimento Desktop
* Organização em Camadas
* Controle de Acesso

---

# Roadmap

## Próximas Evoluções

* Implementação de IA agêntica
* Dashboard gerencial
* Relatórios avançados
* Controle de fornecedores
* Controle de compras
* Multiempresa
* API pública
* Aplicação Web
* Aplicação Mobile
* Auditoria completa

---

# Diferenciais Técnicos

* Separação completa entre interface e regras de negócio
* API REST independente
* Banco centralizado
* Controle de permissões
* Integração com impressora térmica
* Controle de rastreabilidade por lote
* Gestão financeira integrada

---

# Impacto do Projeto

O GastroSynq foi desenvolvido para resolver problemas reais enfrentados por operações de alimentação, consolidando múltiplos processos em uma única plataforma integrada.

Além da implementação funcional, o projeto serviu como laboratório para aplicação prática de conceitos de arquitetura de software, segurança, banco de dados, integração de sistemas e desenvolvimento full stack.

---

# Observação

Este repositório tem caráter demonstrativo e de portfólio.

Por conter regras de negócio e componentes utilizados em ambiente real, o código-fonte completo não é disponibilizado publicamente.

A documentação apresentada busca demonstrar arquitetura, decisões técnicas, tecnologias utilizadas e competências aplicadas durante o desenvolvimento do sistema.
