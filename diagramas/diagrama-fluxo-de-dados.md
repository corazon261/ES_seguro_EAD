# Diagrama de fluxo de dados — Pedido no RapidoFood

Mostra, em ordem, o caminho dos dados desde o login do cliente até a entrega. O
código Mermaid abaixo é o **arquivo-fonte**; o GitHub o renderiza como imagem.
Uma imagem PNG de backup está em [`imagens/`](../imagens/).

```mermaid
sequenceDiagram
    actor Cliente
    participant Auth as Serviço de autenticação
    participant API as API de pedidos
    participant Pag as Gateway de pagamento
    participant DB as Banco de dados
    participant Restaurante
    participant Entregador

    Cliente->>Auth: 1. login / credenciais
    Auth-->>Cliente: sessão autenticada
    Cliente->>API: 2. monta e envia o pedido
    API->>Pag: 3. solicita cobrança
    Pag-->>API: 4. confirmação do pagamento
    API->>DB: 5. registra pedido e dados
    API->>Restaurante: 6. envia pedido
    API->>Entregador: 7. corrida + endereço de entrega
    Entregador->>API: 8. localização em tempo real
    API-->>Cliente: 9. status e mapa da entrega
```
