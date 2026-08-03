# Diagrama de casos de abuso — RapidoFood

Relaciona os atores maliciosos, os casos de abuso (CA01–CA04) e as categorias
STRIDE envolvidas. O código Mermaid abaixo é o **arquivo-fonte**; o GitHub o
renderiza como imagem.

```mermaid
flowchart TB
    Atacante{{Atacante externo}}
    UsuarioMal{{Usuário / entregador mal-intencionado}}

    CA01[CA01 — Sequestro de conta e escalonamento]
    CA02[CA02 — Alteração indevida de pedido]
    CA03[CA03 — Consulta indevida de dados de terceiros]
    CA04[CA04 — Negação de serviço]

    Sistema[(Sistema RapidoFood)]

    Atacante --> CA01
    Atacante --> CA04
    UsuarioMal --> CA02
    UsuarioMal --> CA03

    CA01 -->|Spoofing · Elevation of Privilege · Information Disclosure| Sistema
    CA02 -->|Tampering · Repudiation| Sistema
    CA03 -->|Information Disclosure| Sistema
    CA04 -->|Denial of Service| Sistema
```
