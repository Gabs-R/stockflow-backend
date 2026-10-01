## Módulo 1: Identidade, Contas e Contexto (IAM & RBAC)

### UC-01: Cadastro de Usuário e Criação de Organização

**Atores:** Comprador / Vendedor / Dono de Oficina

**Requisitos Vinculados:** RF-001, RF-003 | **Regras:** RN-IAM-001, RN-IAM-003

```gherkin
Funcionalidade: Cadastro de Usuário e Organização
  Como um profissional do setor automotivo
  Eu quero me registrar e cadastrar minha autopeças
  Para comercializar componentes na plataforma em nome do meu negócio

  Cenário: Cadastro bem-sucedido de pessoa física e oficina
    Dado que o e-mail "contato@silvaauto.com.br" e CPF "123.456.789-00" não existem no banco
    Quando o usuário envia uma requisição POST para "/api/v1/auth/register" com dados cadastrais válidos
    Então a API deve retornar status HTTP 201 Created
    E criar o registro na tabela "users" com a senha criptografada em hash
    Quando o usuário autenticado envia um POST para "/api/v1/organizations" com CNPJ "12.345.678/0001-90"
    Então a API deve criar o registro na tabela "organizations"
    E vincular o criador na tabela "organization_members" com a role "ADMIN"
```

## Módulo 2: Catálogo, Anúncios e Compatibilidade Veicular

### UC-02: Publicação de Anúncio e Criação Atômica de Catálogo

**Atores:** Vendedor (Pessoa Física ou Membro de Organização)

**Requisitos Vinculados:** RF-006, RF-007 | **Regras:** RN-CAT-001, RN-CAT-003

```gherkin
Funcionalidade: Publicação de Anúncios e Catálogo
  Como um vendedor credenciado
  Eu quero publicar uma peça informando dados técnicos e comerciais
  Para disponibilizá-la no marketplace para compradores compatíveis

  # Criação atômica quando o produto base não existe
  Cenário: Criação simultânea de produto mestre e oferta comercial (RF-006)
    Dado que o usuário está autenticado com token JWT válido
    E não existe produto cadastrado com o part_number "BOSCH-0124-ALT"
    Quando o usuário envia um POST para "/api/v1/listings/atomic" com dados do produto e da oferta
    Então o sistema deve persistir em uma única transação o registro em "products" e em "listings"
    E a resposta deve retornar status HTTP 201 Created
    E o anúncio deve assumir o status inicial "ACTIVE" se stock_quantity > 0

  # Validação de estoque mínimo (RN-CAT-003)
  Cenário: Tentativa de ativação de anúncio sem estoque
    Dado que o usuário está autenticado
    Quando o usuário tenta cadastrar um anúncio com stock_quantity = 0 e status "ACTIVE"
    Então a API deve recusar a operação com status HTTP 422 Unprocessable Entity
    E o corpo do erro RFC 7807 deve especificar que anúncios ativos exigem estoque mínimo de 1 unidade
```

### UC-03: Busca Combinada por Compatibilidade e Localização

**Atores:** Comprador / Mecânico

**Requisitos Vinculados:** RF-008, RF-009, RF-010 | **Regras:** RN-CAT-001, RN-CAT-002

```gherkin
Funcionalidade: Mecanismo de Busca Combinada
  Como um comprador buscando peças de reposição
  Eu quero filtrar anúncios combinando meu veículo exato e minha localidade
  Para garantir que a peça serve no meu carro e avaliar opções de retirada próxima

  Cenário: Busca bem-sucedida por compatibilidade e localização
    Dado que existem veículos canônicos cadastrados na hierarquia Marca -> Modelo -> Versão/Ano
    E o produto id 55 possui compatibilidade confirmada com o veículo id 12 ("Civic 2018 2.0")
    E existem anúncios ativos para o produto 55 na cidade "Campinas" e estado "SP"
    Quando o usuário envia um GET para "/api/v1/listings/search?vehicle_id=12&state=SP&city=Campinas"
    Então a API deve retornar status HTTP 200 OK com a listagem paginada
    E todos os itens retornados devem possuir status "ACTIVE" e vínculo comprovado com o veículo 12
    E a lista não deve exibir anúncios de itens incompatíveis ou com estoque zerado
```

## Módulo 3: Pedidos, Itens e Concorrência de Estoque

### UC-04: Reserva Atômica de Pedido com Lock Pessimista

- **Atores:** Comprador / Sistema[cite: 1, 2]
- **Requisitos Vinculados:** RF-011, RF-012, RF-013, RF-014, RF-017[cite: 1, 2]
- **Regras de Negócio:** RN-ORD-001, RN-ORD-002, RN-ORD-003, RN-ORD-006, RN-ORD-007[cite: 1, 2]

```gherkin
Funcionalidade: Criação de Pedido e Reserva Concorrente de Estoque
  Como um comprador na plataforma
  Eu quero reservar uma autopeça de um vendedor
  Para garantir o estoque e o preço da peça antes da retirada física

  # Cenário 1: Concorrência simultânea sobre a última unidade (Prevenção de Overselling)
  Cenário: Tentativa de compra simultânea da última unidade disponível em estoque
    Dado que existe um anúncio id 10 com status "ACTIVE", preço 250.00 e stock_quantity = 1
    E dois compradores distintos ("Comprador A" e "Comprador B") enviam ordens de compra concorrentes para o item 10
    Quando as transações são executadas no banco com bloqueio pessimista via SELECT FOR UPDATE
    Então a requisição do Comprador A deve ser confirmada com status HTTP 201 Created
    E o anúncio id 10 deve ter seu stock_quantity decrementado para 0
    E o status do anúncio id 10 deve transitar automaticamente para "OUT_OF_STOCK"
    E a requisição do Comprador B deve sofrer rollback imediato e retornar HTTP 409 Conflict padronizado via RFC 7807

  # Cenário 2: Imutabilidade do valor histórico registrado
  Cenário: Congelamento histórico do preço do pedido contra reajustes posteriores do vendedor
    Dado que um comprador realizou um pedido para o item id 10 com preço unitário de 250.00
    Quando o vendedor reajusta posteriormente o preço do anúncio id 10 para 300.00
    Então o valor registrado na coluna "order_items.unit_price" do pedido já criado deve permanecer inalterado em 250.00
```

### UC-05: Ciclo de Vida do Pedido, Cancelamento e Retirada Presencial

- **Atores:** Comprador / Vendedor[cite: 1, 2]
- **Requisitos Vinculados:** RF-015, RF-016[cite: 1, 2]
- **Regras de Negócio:** RN-ORD-004, RN-ORD-005, RN-ORD-008[cite: 1, 2]

```gherkin

Funcionalidade: Ciclo de Vida, Estorno e Conclusão de Pedido
  Como participante de uma transação comercial
  Eu quero gerenciar o status do pedido de compra
  Para concretizar a retirada presencial ou liberar o estoque em caso de desistência

  # Cenário 1: Cancelamento com estorno transacional e restauração de anúncio
  Cenário: Cancelamento de pedido com estorno automático de estoque e reativação do anúncio
    Dado que existe um pedido id 50 com status "PENDING" contendo 2 unidades do anúncio id 12
    E o anúncio id 12 está com stock_quantity = 0 e status "OUT_OF_STOCK"
    Quando o comprador envia uma requisição POST para "/api/v1/orders/50/cancel" informando a justificativa
    Então o status do pedido id 50 deve transitar para "CANCELLED"
    E a coluna "cancellation_reason" deve ser preenchida com o motivo fornecido
    E o estoque do anúncio id 12 deve sofrer incremento atômico de 2 unidades
    E o status comercial do anúncio id 12 deve retornar automaticamente para "ACTIVE"

  # 🚨 [NOVO / ADICIONADO RECENTEMENTE] Cenário 2: Conclusão física e imutabilidade de pedido finalizado
  Cenário: Conclusão bem-sucedida da retirada presencial e encerramento do ciclo
    Dado que existe um pedido id 70 no estado "CONFIRMED"
    Quando o vendedor marca o pedido como disponível para retirada via POST para "/api/v1/orders/70/ready"
    Então o status do pedido id 70 deve transitar para "READY_FOR_PICKUP"
    Quando o comprador realiza a retirada presencial e confirma o recebimento via POST para "/api/v1/orders/70/complete"
    Então o status do pedido deve transitar para "COMPLETED"
    E o pedido torna-se terminal e imutável, rejeitando qualquer tentativa posterior de cancelamento ou estorno
```
```