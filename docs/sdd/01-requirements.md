## Modulo 1: Identidade e Organizações

Context: Users, Profiles & Organizations

- **RF-001**: O sistema deve permitir o cadastro de novos usuários contendo nome completo, CPF, telefone, e-mail, senha e localização base (CEP, cidade e UF)[cite: 1].
- **RF-002**: O sistema deve autenticar usuários via e-mail e senha, emitindo um token de acesso JWT[cite: 1, 2].
- **RF-003**: O sistema deve permitir que um usuário crie uma ou mais Organizações (empresas, oficinas ou lojas) informando razão social, nome fantasia, CNPJ, telefone e localização (CEP, cidade e UF)[cite: 1].
- **RF-004**: O sistema deve permitir a inclusão de outros usuários como membros de uma Organização, atribuindo um dos seguintes papéis (Roles): ADMIN, SELLER ou INVENTORY_MANAGER[cite: 1, 2].
- **RF-005**: O usuário deve poder alternar seu contexto de operação ativa entre "Conta Pessoal" e qualquer "Organização" da qual seja membro ativo[cite: 1, 2].

### Modulo 2: Catalogo, Anuncios e Compatibilidade veicular

- **RF-006**: O sistema deve permitir a criação simultânea de um produto base (PRODUCT) e de sua oferta comercial (LISTING) em uma única transação, caso o item ainda não exista no catálogo global[cite: 1, 3].
- **RF-007**: O sistema deve permitir a publicação de um anúncio (LISTING) contendo preço unitário, condição física (NEW ou USED), quantidade em estoque e vínculo explícito ao contexto ativo (Conta Pessoal ou Organização)[cite: 1, 3].
- **RF-008**: O sistema deve manter um catálogo canônico de veículos, normalizado hierarquicamente por Marca, Modelo, Versão do Motor e intervalo de Anos de fabricação[cite: 1, 3].
- **RF-009**: O sistema deve permitir que múltiplos veículos da base canônica sejam associados a um PRODUCT para atestar a compatibilidade veicular do item[cite: 1, 3].
- **RF-010**: A API de busca deve permitir consultas combinadas por

---

### Módulo 3: Pedidos, Itens e Concorrência de Estoque

- **RF-011**: O sistema deve permitir que um comprador autenticado crie um pedido (`ORDER`) contendo um ou mais itens (`ORDER_ITEM`) a partir de anúncios ativos (`LISTING` com status `ACTIVE`).
- **RF-012**: O sistema deve capturar e congelar o preço unitário histórico (`unit_price`) no momento da criação do item, tornando o valor cobrado imune a reajustes posteriores do vendedor.
- **RF-013**: O sistema deve decrementar a quantidade em estoque (`stock_quantity`) de forma atômica no ato da criação do pedido, utilizando bloqueio transacional pessimista.
- **RF-014**: O sistema deve alterar automaticamente o status do anúncio para `OUT_OF_STOCK` assim que seu estoque atingir zero.
- **RF-015**: O sistema deve permitir a transição controlada do pedido pelos estados: `PENDING`, `CONFIRMED`, `READY_FOR_PICKUP` e `COMPLETED`.
- **RF-016**: O sistema deve permitir o cancelamento do pedido (`CANCELLED`) caso ainda não tenha sido concluído, estornando transacionalmente a quantidade reservada para o estoque do anúncio.
- **RF-017**: O sistema deve rejeitar a criação de pedidos sem itens ou que contenham anúncios pertencentes a mais de um vendedor/organização, garantindo que o cabeçalho do pedido reflita exatamente um único vendedor responsável.