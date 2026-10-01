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
* [`docs/sdd/01-requirements.md`](docs/sdd/01-requirements.md): Requisitos Funcionais (RF-001 a RF-017) e Requisitos Não-Funcionais (RNFs).
* [`docs/sdd/02-business-rules.md`](docs/sdd/02-business-rules.md): Invariantes de domínio, matriz RBAC e regras de integridade (RN-IAM, RN-CAT e RN-ORD).
* [`docs/sdd/03-use-cases.md`](docs/sdd/03-use-cases.md): Casos de uso e critérios de aceite em formato BDD (UC-01 a UC-05).
* [`docs/database/ddl_consolidated.sql`](docs/database/ddl_consolidated.sql): DDL físico unificado em MySQL 8 (InnoDB) para os Módulos 1, 2 e 3.

---

## 🗺️ Roadmap de Módulos (Fase 1: SDD & Planejamento)

- [x] **Módulo 1: Identidade, IAM e Perfis**
  * Cadastro de usuários, organizações (oficinas/lojas) e controle de acesso baseado em papéis (RBAC).
- [x] **Módulo 2: Catálogo, Anúncios e Compatibilidade Canônica**
  * Desacoplamento `PRODUCT` vs. `LISTING`, modelo canônico de veículos e busca relacional com herança de compatibilidade.
- [x] **Módulo 3: Pedidos, Itens e Concorrência de Estoque**
  * Ciclo de vida transacional de `ORDER`, histórico imutável de preços e prevenção de *overselling* via lock pessimista (`SELECT ... FOR UPDATE`).
- [ ] **Módulo 4: Contratos de API & Schemas (Fase Atual)**
  * Endpoints RESTful, validação com Pydantic V2, matriz de autorização e padronização RFC 7807 (*Problem Details*).
- [ ] **Módulo 5: Setup de Infraestrutura & Banco**
  * Docker Compose (FastAPI + MySQL 8), inicialização do SQLAlchemy e versionamento com Alembic.