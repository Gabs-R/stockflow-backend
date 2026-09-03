# StockFlow - Automotive Parts Marketplace

> Marketplace especializado no setor automotivo projetado com foco em busca canônica por compatibilidade veicular, conectando clientes, oficinas mecânicas e autopeças.

O projeto adota a metodologia **SDD (Specification-Driven Development)**, garantindo que invariantes de domínio, modelos relacionais e contratos de API sejam formalizados e testados antes da escrita do código de produção.

---

## 🛠️ Stack Tecnológica Planejada

* **Linguagem & Framework:** Python 3.11 & FastAPI
* **Banco de Dados:** MySQL 8 (InnoDB Engine)
* **ORM & Migrations:** SQLAlchemy & Alembic
* **Validação de Schemas:** Pydantic V2
* **Containers:** Docker & Docker Compose
* **Qualidade & Testes:** Pytest

---

## 📐 Estrutura e Engenharia (SDD)

Os contratos de domínio e especificações técnicas estão versionados no diretório `docs/`:
* [`docs/sdd/01-requirements.md`](docs/sdd/01-requirements.md): Requisitos Funcionais (RFs) rastreáveis dos Módulos 1 (IAM) e 2 (Catálogo).
* [`docs/sdd/02-business-rules.md`](docs/sdd/02-business-rules.md): Invariantes, matriz RBAC e regras de herança de compatibilidade.
* [`docs/database/ddl_modules_1_and_2.sql`](docs/database/ddl_modules_1_and_2.sql): DDL físico otimizado para MySQL 8 com chaves primárias `BIGINT UNSIGNED` e índices compostos.

---

## 🤖 Arquitetura Multi-Agente (Antigravity Kit)

Este repositório integra um ambiente de desenvolvimento autônomo baseado em agentes em [`.agent/`](.agent/):
* **20 Agentes Especialistas** cobrindo Engenharia de Software, Arquitetura de Dados, Segurança e QA.
* **36 Módulos de Habilidade (Skills)** com regras formais de Clean Code, padrões de API e modelagem de banco.
* **11 Workflows Automatizados** (`/plan`, `/orchestrate`, `/brainstorm`, etc.).
* Documentação técnica completa disponível em [`.agent/ARCHITECTURE.md`](.agent/ARCHITECTURE.md).

---

## 🗺️ Roadmap de Módulos

- [x] **Módulo 1:** Identidade, contas e organizações (IAM & RBAC Híbrido)
- [x] **Módulo 2:** Catálogo, Anúncios (`PRODUCT` vs `Listing`) e Catálogo Canônico de Veículos
- [ ] **Módulo 3:** Contratos de API (RFC 7807) e Schemas Pydantic
- [ ] **Módulo 4:** Motor de Busca Canônica e Compatibilidade Veicular
- [ ] **Módulo 5:** Setup de infraestrutura Docker e configuração do SQLAlchemy/Alembic
