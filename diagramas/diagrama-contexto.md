# Diagrama de contexto — RapidoFood

Mostra os usuários, a plataforma (componentes internos) e os serviços externos.
O código Mermaid abaixo é o **arquivo-fonte**; o GitHub o renderiza como imagem.

```mermaid
flowchart TB
    Cliente([Cliente])
    Restaurante([Restaurante / Loja])
    Entregador([Entregador])
    Admin([Administrador])

    subgraph RapidoFood[Plataforma RapidoFood]
        App[Aplicativo móvel / Portal web]
        API[Aplicação / API]
        Auth[Serviço de autenticação]
        DB[(Banco de dados)]
    end

    Pagamento[[Gateway de pagamento]]
    Mapas[[Serviço de mapas / geolocalização]]
    Notif[[Serviço de notificações]]

    Cliente --> App
    Restaurante --> App
    Entregador --> App
    Admin --> App

    App --> API
    API --> Auth
    API --> DB
    API --> Pagamento
    API --> Mapas
    API --> Notif
```
