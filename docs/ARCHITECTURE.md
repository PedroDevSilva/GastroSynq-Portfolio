# ARCHITECTURE.md

# Arquitetura do Sistema — GastroSynq

## Visão Geral Arquitetural

O GastroSynq foi projetado utilizando uma arquitetura cliente-servidor com separação clara entre interface de usuário, regras de negócio e persistência de dados.

A solução foi construída com foco em:

* Manutenibilidade
* Escalabilidade
* Organização modular
* Segurança
* Reutilização de código
* Facilidade de evolução

A aplicação é composta por três camadas principais:

1. Cliente Desktop (WinForms)
2. API Backend (Spring Boot)
3. Banco de Dados PostgreSQL

---

## Arquitetura Geral

```text
┌──────────────────────────────┐
│         Usuário Final         │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│        Frontend Desktop       │
│                               │
│ C# + WinForms + Krypton       │
│                               │
│ • Login                       │
│ • PDV                         │
│ • Estoque                     │
│ • Caixa                       │
│ • Relatórios                  │
│ • Etiquetas                   │
└──────────────┬───────────────┘
               │ HTTPS / JSON
               ▼
┌──────────────────────────────┐
│        Backend API           │
│                              │
│ Java + Spring Boot           │
│                              │
│ • Controllers                │
│ • Services                   │
│ • Security                   │
│ • JWT                        │
│ • Regras de negócio          │
└──────────────┬───────────────┘
               │ JPA/Hibernate
               ▼
┌──────────────────────────────┐
│       PostgreSQL             │
│                              │
│ • Funcionários               │
│ • Produtos                   │
│ • Estoque                    │
│ • Vendas                     │
│ • Caixa                      │
│ • Pagamentos                 │
│ • Etiquetas                  │
└──────────────────────────────┘
```

---

# Arquitetura Cliente-Servidor

## Cliente

Responsável pela experiência do usuário.

Implementado em:

* C#
* Windows Forms
* Krypton Toolkit

Funções:

* Coleta de dados
* Validação básica
* Consumo da API
* Exibição de informações
* Operações de PDV
* Operações administrativas

Toda regra crítica permanece no backend.

---

## Servidor

Responsável pela lógica de negócio.

Implementado em:

* Java
* Spring Boot

Funções:

* Autenticação
* Autorização
* Processamento de vendas
* Controle de estoque
* Controle de caixa
* Relatórios
* Persistência

---

## Banco de Dados

Responsável pelo armazenamento centralizado.

Tecnologias:

* PostgreSQL
* Supabase PostgreSQL

Funções:

* Persistência de dados
* Integridade relacional
* Histórico operacional
* Consistência transacional

---

# Separação de Responsabilidades

## Frontend

Responsável por:

* Interface gráfica
* Navegação
* Consumo de APIs
* Feedback ao usuário

Não possui responsabilidade sobre:

* Segurança
* Persistência
* Regras críticas

---

## Backend

Responsável por:

* Regras de negócio
* Segurança
* Controle transacional
* Persistência

Não possui responsabilidade sobre:

* Interface visual

---

## Banco

Responsável por:

* Armazenamento
* Relacionamentos
* Integridade dos dados

---

# Camadas da Aplicação

## Controller Layer

Recebe requisições HTTP.

Responsabilidades:

* Receber dados
* Validar entrada
* Encaminhar para Services
* Retornar respostas

Exemplos:

* AuthController
* ProdutoController
* VendaController
* CaixaController

---

## Service Layer

Camada principal de negócio.

Responsabilidades:

* Processamento das regras
* Validações
* Orquestração

Exemplos:

* AuthService
* VendaService
* CaixaService
* EstoqueService

---

## Repository Layer

Responsável pelo acesso aos dados.

Tecnologias:

* Spring Data JPA
* Hibernate

Responsabilidades:

* CRUD
* Consultas
* Paginação
* Filtros

---

## Database Layer

Responsável pela persistência.

Tecnologias:

* PostgreSQL

---

# Fluxo de Dados

## Exemplo: Realização de Venda

```text
Operador

▼

PDV Desktop

▼

VendaController

▼

VendaService

▼

Validação dos produtos

▼

Validação do caixa

▼

Criação da venda

▼

Criação dos itens

▼

Criação do pagamento

▼

Atualização do estoque

▼

Persistência PostgreSQL

▼

Resposta para o Desktop
```

---

# Fluxo de Autenticação

```text
Usuário

▼

Tela Login

▼

POST /auth/login

▼

AuthService

▼

Validação senha BCrypt

▼

Geração JWT

▼

Retorno Token

▼

Armazenamento local

▼

Uso nas próximas requisições
```

---

# Arquitetura de Segurança

## Autenticação

Tecnologia:

* JWT

Objetivo:

* Sessões stateless
* Escalabilidade
* Menor acoplamento

---

## Senhas

Tecnologia:

* BCrypt

Características:

* Hash irreversível
* Salt automático
* Proteção contra ataques de força bruta

---

## Controle de Acesso

Baseado em:

* Roles
* Níveis de permissão

Exemplos:

* Administrador
* Gerente
* Operador

---

# Integração com Impressão de Etiquetas

Arquitetura:

```text
Sistema

▼

Gerador de Etiqueta

▼

Imagem PNG

▼

Cliente Niimbot

▼

Impressora Niimbot B1
```

Objetivo:

* Separar lógica de negócio da lógica de impressão.
* Permitir troca futura de fabricante.

---

# Decisões Arquiteturais

## WinForms ao invés de Web

Motivação:

* Operação local
* Facilidade de implantação
* Baixo custo operacional
* Ambiente controlado

Trade-off:

* Dependência de Windows

---

## Spring Boot

Motivação:

* Produtividade
* Ecossistema robusto
* Segurança integrada
* Grande adoção corporativa

---

## PostgreSQL

Motivação:

* Banco relacional robusto
* Integridade transacional
* Escalabilidade

---

## JWT

Motivação:

* Autenticação desacoplada
* Escalabilidade horizontal
* Menor carga no servidor

---

# Benefícios da Arquitetura

A arquitetura atual proporciona:

* Separação clara de responsabilidades
* Facilidade de manutenção
* Evolução modular
* Segurança centralizada
* Escalabilidade do backend
* Reutilização de componentes
* Baixo acoplamento

---

# Trade-offs Assumidos

| Decisão              | Benefício                  | Trade-off                                      |
| -------------------- | -------------------------- | ---------------------------------------------- |
| WinForms             | Rapidez de desenvolvimento | Dependência de Windows                         |
| Monólito Spring Boot | Simplicidade operacional   | Escalabilidade limitada em módulos específicos |
| PostgreSQL único     | Consistência transacional  | Possível gargalo futuro                        |
| JWT                  | Escalabilidade             | Necessidade de gestão de expiração             |

---

# Possíveis Evoluções

## Curto Prazo

* Refresh Tokens
* Logs centralizados
* Auditoria de operações
* Backup automatizado

---

## Médio Prazo

* Dashboard gerencial
* Indicadores financeiros
* Controle avançado de estoque
* Relatórios analíticos

---

## Longo Prazo

* Microsserviços
* Aplicação Web
* Aplicativo Mobile
* Integração fiscal
* Integração com marketplaces
* Multiempresa
* Multiunidade

---

# Considerações Arquiteturais Finais

O GastroSynq foi concebido seguindo princípios de engenharia de software utilizados em aplicações corporativas:

* Arquitetura em camadas
* Separação de responsabilidades
* Controle centralizado de regras de negócio
* Segurança baseada em JWT
* Persistência relacional robusta
* Organização orientada à manutenção

Mesmo sendo um projeto desenvolvido em contexto acadêmico/profissional inicial, sua estrutura busca refletir práticas utilizadas em sistemas empresariais reais, demonstrando domínio de conceitos de backend, banco de dados, autenticação, integração e arquitetura de software.
