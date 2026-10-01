### Modulo 1: Identidade, contas e organizações (IAM)

**RN-IAM-001 (Unicidade)**: O e-mail e o CPF de um usuário devem ser únicos no sistema. O CNPJ de uma organização deve ser único.

**RN-IAM-002 (Membros)**: Um usuário não pode ser adicionado em duplicidade à mesma organização com múltiplos papéis simultâneos.

**RN-IAM-003 (Papel Criador)**: O usuário que cria a organização recebe automaticamente o papel de `ADMIN`.  

**RN-IAM-004 (Autorização RBAC)**:
• `ADMIN`: Gerencia membros (convidar/remover), edita dados da organização, cria/edita anúncios e gerencia pedidos.  
• `SELLER`: Cria anúncios, gerencia pedidos e interage com compradores em nome da organização.  
• `INVENTORY_MANAGER`: Cria e edita anúncios/estoque da organização, mas não pode convidar membros nem cancelar pedidos alheios.  

### Modulo 2: Catalogo, Anuncios e Compatibilidade

- **RN-CAT-001 (Separação Product vs Listing)**: O produto mestre (PRODUCT) representa a peça técnica no catálogo global; o anúncio (LISTING) representa a oferta comercial do vendedor (preço, condição e estoque)[cite: 1, 3].
- **RN-CAT-002 (Catálogo Canônico de Veículos)**: A compatibilidade veicular deve ser baseada estritamente em tabelas mestres estruturadas (Marca, Modelo, Veículo). É proibido o uso de campos livres de texto para garantir a precisão das consultas[cite: 1, 3].
- **RN-CAT-003 (Herança de Compatibilidade)**: A aplicação veicular pertence ao produto base (PRODUCT); múltiplos anúncios do mesmo item herdam essa compatibilidade automaticamente[cite: 1, 3].
- **RN-CAT-004 (Máquina de Estados de Anúncios)**: Ciclo de vida definido: `DRAFT` ➔ `ACTIVE` ➔ `PAUSED` ➔ `OUT_OF_STOCK`[cite: 1, 3].

### Módulo 3: Pedidos e Concorrência de Estoque (ORD)

- **RN-ORD-001 (Prevenção de *Overselling* / Lock Pessimista)**:
A criação do pedido exige a abertura de transação com **bloqueio pessimista** (`SELECT ... FOR UPDATE`) sobre a linha do anúncio na tabela `listings`. Caso `quantidade_solicitada > stock_quantity`, a transação sofre rollback imediato e a API retorna HTTP 409 Conflict padronizado via RFC 7807.
    - **Bloqueio pessimista → Permitir com que o acesso seja restrito para uma parte que tentar acessa-la em caso de acesso simultâneo**
    - HTTP 409 Conflit - O motivo de termos colocado, foi devido a melhor para mostrar que o pedido não pode ser completado por conta de um conflito com o estado atual do recurso de destino no servidor.
- **RN-ORD-002 (Congelamento Histórico de Preço)**:
O valor registrado em `order_items.unit_price` é estritamente imutável. Alterações comerciais no anúncio original não produzem efeito cascata sobre pedidos existentes.
- **RN-ORD-003 (Invariante de Autocompra)**:
Um usuário não pode comprar itens dos seus próprios anúncios pessoais nem de anúncios pertencentes a organizações onde possua papel ativo (`ADMIN`, `SELLER` ou `INVENTORY_MANAGER`).
- **RN-ORD-004 (Autorização de Transição de Ciclo de Vida)**:
    - `PENDING` ➔ `CONFIRMED`: Apenas o vendedor responsável (ou membro com papel `ADMIN`/`SELLER` da organização do anúncio).
    - `CONFIRMED` ➔ `READY_FOR_PICKUP`: Apenas o vendedor responsável ou organização.
    - `READY_FOR_PICKUP` ➔ `COMPLETED`: Comprador ou vendedor (confirmação da entrega física).
    - Qualquer estado antes de `COMPLETED` ➔ `CANCELLED`: Comprador ou vendedor.
- **RN-ORD-005 (Estorno e Reativação Automática)**:
Ao transitar para `CANCELLED`, o estoque é estornado atomicamente ao anúncio de origem. Se o anúncio estiver com status `OUT_OF_STOCK`, ele retorna automaticamente para `ACTIVE`.
- **RN-ORD-006 (Invariante de Pedido Mono-Vendedor / Single Seller)**:
Um pedido (`ORDER`) só pode conter itens (`order_items`) associados ao mesmo vendedor ou organização de origem. É estritamente proibido criar um pedido contendo anúncios de vendedores distintos em uma mesma transação. Caso o comprador tente finalizar um carrinho misto, a operação deve ser segmentada em pedidos independentes por vendedor.
- **RN-ORD-007 (Consistência Matemática e Valor Mínimo)**:
Todo pedido deve conter no mínimo 1 item com `quantity >= 1`. O campo `orders.total_amount` deve corresponder estritamente ao somatório aritmético de (unit_price * quantity) de todos os itens filhos no momento da criação, calculado no backend e nunca recebido pronto do frontend.
- **RN-ORD-008 (Auditoria de Cancelamento)**:
A transição para `CANCELLED` exige o preenchimento obrigatório da justificativa em `cancellation_reason`, documentando a parte que originou o cancelamento (comprador ou vendedor) e o motivo da desistência. Após atingir o estado `COMPLETED`, o pedido é considerado terminal e não aceita cancelamento.