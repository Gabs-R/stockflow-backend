# StockFlow - Automotive Parts Marketplace

> Marketplace especializado no setor automotivo projetado com foco em busca canonica por compatibilidade veicular, conectando clientes, oficinas mecanicas e autopecas

O projeto adota a metodologia **SDD  (Specification-Driven-Development)**, garantindo que invariantes de dominio, modelos relacionais e contratos de API sejam formalizados e testados antes da escrita do código de produção.

# 🛠️ Stack Tecnologia Planejada 

* **Linguagem & Framework:** Python 3.11 & FastAPI
* **Banco de dados:** MySQL 8, (InnoDB Engine)
* **ORM & Migrations:** SQLAlchemy & Alembic
* **Validacao de Schemas:** Pydantic V2
* **Containers:** Docker & Docker Compose
* **Qualidade & Testes:** Pytest

--- 

## 📐 Estrutura e Engenharia (SDD)

Os contratos de domínio e específicações técnicas estão versionados no diretório `docs/`:
* `docs/sdd/01-requirements.md`: Requisitos Funcionais (RFs) rastreáveis dos Módulos 1 (IAM) e 2 (Catálogo).
* `docs/sdd/02-business-rules.md`: Invariantes, matriz RBAC e regras de herança de compatibilidade.
* `docs/database/ddl_modules_1_and_2.sql`: DDL físico otimizado para MySQL 8 com chaves primárias `BIGINT UNSIGNED` e índices compostos.

--- 

## Roadmap de Modulos

- [x] **Modulo 1:** Identidade, contas e organizacoes (IAM & RBAC Hibrido)
- [x] **Modulo 2:** Catalogo, Anuncios (`PRODUCT` vs `Listening`) e Catalogo Canonico de Veiculos
- [ ] **Modulo 3:** Contratos de API (RFC 7807) e Schemas Pydantic
- [ ] **Modulo 4:** Contratos de API (RFC 7807) e Schemas Pydantic 
- [ ] **Modulo 5:** Setup de infraestrutura Docker e configuracao do SQLAlchemy/ALembic