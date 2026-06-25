# RECRUITER_SUMMARY.md

# GastroSynq — Executive Summary

## Resumo Executivo

O GastroSynq é um sistema ERP para restaurantes desenvolvido utilizando arquitetura cliente-servidor, com frontend desktop em C# WinForms e backend REST em Java Spring Boot.

O projeto foi concebido para atender operações reais de restaurantes, centralizando processos de vendas, estoque, rastreabilidade de produtos, controle financeiro e gestão operacional.

Embora o código-fonte não esteja disponível publicamente por razões comerciais, este repositório documenta as decisões arquiteturais, desafios técnicos e soluções implementadas durante o desenvolvimento.

---

# Escopo do Projeto

O sistema foi projetado para resolver problemas comuns encontrados em operações gastronômicas:

* Controle manual de estoque
* Falta de rastreabilidade de produtos
* Controle inadequado de validade
* Ausência de histórico operacional
* Dificuldade de fechamento de caixa
* Processos manuais de etiquetagem
* Baixa integração entre setores

---

# Principais Tecnologias Utilizadas

## Frontend

* C#
* .NET
* Windows Forms
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

* Supabase PostgreSQL
* Git
* GitHub

## Integrações

* Impressoras térmicas Niimbot
* Geração de PDFs
* APIs REST

---

# Principais Problemas Resolvidos

## Controle de Estoque

Implementação de controle de estoque com:

* Entradas
* Saídas
* Movimentações
* Histórico operacional

---

## Controle de Lotes

Produtos podem ser rastreados individualmente através de lotes.

Benefícios:

* Identificação rápida
* Auditoria
* Controle de validade

---

## Controle de Validade

Implementação de regras de validade por estado operacional do produto.

Exemplos:

* Produto Cru
* Produto Preparado
* Produto Refrigerado

O sistema calcula automaticamente a validade operacional.

---

## Rastreabilidade

Todas as alterações relevantes podem ser auditadas através de registros históricos.

Isso permite:

* Identificar responsáveis
* Rastrear alterações
* Investigar ocorrências

---

## Operação de PDV

Desenvolvimento de módulo de vendas contendo:

* Carrinho
* Pagamentos
* Fechamento de venda
* Histórico
* Consulta detalhada

---

## Controle Financeiro

Implementação de:

* Abertura de caixa
* Fechamento de caixa
* Histórico de caixas
* Relatórios operacionais

---

# Competências Demonstradas

## Arquitetura de Software

* Arquitetura cliente-servidor
* Separação de responsabilidades
* Organização em camadas
* Modularização

---

## Backend Engineering

* Desenvolvimento de APIs REST
* Segurança com JWT
* Controle de autenticação
* Controle de autorização
* Persistência com JPA/Hibernate

---

## Banco de Dados

* Modelagem relacional
* Integridade referencial
* Normalização
* Consultas relacionais
* Estratégias de rastreabilidade

---

## Desktop Development

* Interfaces WinForms
* Eventos
* Consumo de APIs REST
* Serialização JSON
* Fluxos operacionais complexos

---

## Integração de Sistemas

* Comunicação Desktop ↔ API
* Impressão de etiquetas
* Geração de documentos
* Integração com dispositivos externos

---

## Engenharia de Software

* Organização de projeto
* Versionamento
* Documentação técnica
* Tratamento de erros
* Validações de negócio

---

# Desafios Técnicos Relevantes

## Sincronização Cliente-Servidor

Desenvolvimento de comunicação segura entre aplicação desktop e backend REST.

---

## Controle de Estado Operacional

Implementação de rastreamento do ciclo de vida dos produtos.

---

## Impressão de Etiquetas

Integração com impressoras Niimbot para geração e envio de etiquetas operacionais.

---

## Segurança

Implementação de:

* Hash de senhas
* JWT
* Controle de acesso por função
* Proteção de endpoints

---

# Complexidade do Projeto

O GastroSynq não é um CRUD simples.

O projeto envolve múltiplos domínios integrados:

* Segurança
* Estoque
* Produção
* Vendas
* Financeiro
* Auditoria
* Rastreabilidade

Além disso, utiliza diferentes tecnologias e padrões arquiteturais para resolver problemas reais de negócio.

---

# Habilidades Evidenciadas

Este projeto demonstra capacidade prática em:

* Engenharia de Software
* Arquitetura de Sistemas
* Desenvolvimento Backend
* Desenvolvimento Desktop
* Modelagem de Banco de Dados
* Integração entre sistemas
* Segurança de aplicações
* Documentação técnica
* Resolução de problemas de negócio

---

# Impacto Profissional

O desenvolvimento do GastroSynq proporcionou experiência prática em:

* Construção de sistemas corporativos
* Levantamento de requisitos
* Modelagem de domínio
* Implementação de regras de negócio
* Integração de tecnologias distintas
* Desenvolvimento de software voltado para uso real

---

# Observação

Por questões comerciais e de propriedade intelectual, o código-fonte não é disponibilizado publicamente.

Este repositório tem como objetivo apresentar a arquitetura, decisões técnicas e competências desenvolvidas durante a construção da solução.
