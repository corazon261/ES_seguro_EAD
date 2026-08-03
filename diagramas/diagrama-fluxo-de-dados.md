# Diagrama de fluxo de dados — Pedido no RapidoFood

Mostra o caminho dos dados desde o login do cliente até a entrega. O código
Mermaid abaixo é o **arquivo-fonte**; o GitHub o renderiza como imagem.

```mermaid
flowchart LR
    Cliente([Cliente])
    Restaurante([Restaurante])
    Entregador([Entregador])
    Auth[Serviço de autenticação]
    API[API de pedidos]
    Pag[[Gateway de pagamento]]
    DB[(Banco de dados)]

    Cliente -->|1. login / credenciais| Auth
    Cliente -->|2. monta e envia o pedido| API
    API -->|3. solicita cobrança| Pag
    Pag -->|4. confirmação do pagamento| API
    API -->|5. registra pedido e dados| DB
    API -->|6. envia pedido| Restaurante
    API -->|7. corrida + endereço de entrega| Entregador
    Entregador -->|8. localização em tempo real| API
    API -->|9. status e mapa da entrega| Cliente
```
