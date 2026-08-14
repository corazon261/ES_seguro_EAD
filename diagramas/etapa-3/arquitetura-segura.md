# Diagrama de Arquitetura Segura — RapidoFood

**Etapa 3 — Projeto de uma Arquitetura Segura**  
**Responsável:** Lucas Kaue Ribeiro Weber  
**Sistema:** RapidoFood (App de Delivery)

Este documento contém o diagrama de arquitetura segura do RapidoFood, apresentando os componentes do sistema, serviços de autenticação e autorização, banco de dados, auditoria/monitoramento, integrações externas e o posicionamento dos principais controles de segurança definidos para mitigar os riscos críticos e altos (R01, R04 e R06).

---

## Diagrama da Arquitetura Segura (Mermaid)

```mermaid
flowchart TB
    %% Atores / Usuários
    subgraph Atores[" 👥 Atores / Usuários "]
        Cliente(["👤 Cliente"])
        Restaurante(["🏪 Restaurante / Loja"])
        Entregador(["🛵 Entregador"])
        Admin(["🛡️ Administrador"])
    end

    %% Camada de Apresentação / Clientes
    subgraph Interfaces[" 📱 Camada de Apresentação "]
        AppCliente["App Mobile (Cliente / Entregador)"]
        WebAdmin["Painel Web (Restaurante / Admin)"]
    end

    %% Borda de Segurança / API Gateway
    subgraph Perimetro[" 🌐 Camada de Perímetro e Proteção "]
        APIGateway["API Gateway / Reverse Proxy"]
        WAF["WAF & Rate Limiting\n[Controle: Bloqueio de Força Bruta e DoS]"]
    end

    %% Serviços de Backend e Controles de Segurança
    subgraph Backend[" ⚙️ Camada de Aplicação e Serviços de Backend "]
        
        %% Serviço de Autenticação
        subgraph ServicoAuth[" 🔑 Serviço de Autenticação (Auth Service) "]
            AuthAPI["Controlador de Autenticação (/auth/login)"]
            MFAEngine["Mecanismo MFA (OTP via SMS/E-mail)\n[Controle DA01 / RS01 - Mitigação R01]"]
            TokenManager["Gerenciador de Sessão & JWT\n(Assinatura RS256 / Expiração Curta)"]
        end

        %% Serviço de Pedidos e Negócio
        subgraph ServicoPedidos[" 📦 Serviço de Pedidos (Order Service) "]
            OrderAPI["API de Pedidos (/pedidos & /checkout)"]
            ServerValidation["Validação Server-Side de Preços e Taxas\n[Controle DA03 / RS03 - Mitigação R04]"]
            AuthzModule["Módulo de Autorização de Nível de Objeto\n(Verificação dono_id == token.user_id)\n[Controle DA02 / RS02 - Mitigação R06]"]
        end

        %% Serviço de Logs e SIEM
        subgraph Monitoramento[" 📊 Auditoria e Detecção "]
            Logger["Serviço de Logs Estruturados"]
            SIEM["SIEM / Detecção de Intrusões\n(Regras de Detecção em Tempo Real)"]
        end
    end

    %% Camada de Armazenamento
    subgraph Dados[" 🗄️ Camada de Persistência "]
        DB[("PostgreSQL\n(Dados de Usuários, Pedidos e Produtos)\n[Criptografia At-Rest & Queries Parametrizadas]")]
    end

    %% Serviços Externos Integrados
    subgraph Externos[" ☁️ Serviços Externos Integrados "]
        GatewayPagamento[["💳 Gateway de Pagamento Seguro\n(PCI-DSS Compliant)"]]
        ServicoNotif[["✉️ Provedor de Notificações / SMS / E-mail\n(Disparo de Códigos MFA e Alertas)"]]
        ServicoMapas[["🗺️ Serviço de Mapas / Logística"]]
    end

    %% Conexões dos Atores às Interfaces
    Cliente --> AppCliente
    Entregador --> AppCliente
    Restaurante --> WebAdmin
    Admin --> WebAdmin

    %% Interfaces acessam o API Gateway via TLS 1.3
    AppCliente -->|"HTTPS / TLS 1.3"| APIGateway
    WebAdmin -->|"HTTPS / TLS 1.3"| APIGateway

    %% Gateway aplica filtros de segurança
    APIGateway --- WAF

    %% Gateway roteia para Auth e Pedidos
    APIGateway -->|"Requisições de Login / MFA"| AuthAPI
    APIGateway -->|"Requisições de API"| OrderAPI

    %% Fluxos Internos do Auth Service
    AuthAPI --> MFAEngine
    MFAEngine --> TokenManager
    MFAEngine -.->|"Envia código OTP"| ServicoNotif
    AuthAPI -->|"Consulta Credenciais Seguras"| DB
    AuthAPI -->|"Registra tentativas de login"| Logger

    %% Fluxos Internos do Order Service
    OrderAPI --> AuthzModule
    OrderAPI --> ServerValidation
    ServerValidation -->|"Consulta Preços Oficiais"| DB
    AuthzModule -->|"Valida Dono do Pedido"| DB
    OrderAPI -->|"Registra eventos de acesso / divergências"| Logger
    ServerValidation -->|"Envia transação recalculada"| GatewayPagamento
    OrderAPI -->|"Consulta Rotas / Logística"| ServicoMapas

    %% Fluxo de Logs para o SIEM
    Logger --> SIEM
```

---

## Descrição dos Componentes e Controles de Segurança

1. **Atores e Camada de Apresentação:** Clientes, entregadores, restaurantes e administradores comunicam-se com o RapidoFood exclusivamente através de canais criptografados (`HTTPS / TLS 1.3`).
2. **API Gateway & WAF:** Ponto único de entrada com mitigação perimetral. Aplica *Rate Limiting* (limite de requisições por IP/rota) para conter ataques de negação de serviço e força bruta contra o endpoint de autenticação.
3. **Serviço de Autenticação (Auth Service):**
   - **MFA Obrigatório:** Implementa o controle **DA01 / RS01** para mitigar o sequestro de contas (**R01**), enviando códigos de uso único (OTP) via serviço externo de notificações.
   - **Gerenciamento de Tokens JWT:** Emissão de tokens criptograficamente assinados (RS256) com tempo de expiração curto e revogação dinâmica.
4. **Serviço de Pedidos (Order Service):**
   - **Módulo de Autorização de Nível de Objeto:** Implementa o controle **DA02 / RS02** para mitigar a exposição indevida de dados (**R06 / IDOR**), garantindo que apenas o usuário autenticado (`token.user_id`) acesse os dados de seu respectivo pedido (`pedido.cliente_id`).
   - **Validação Server-Side de Preços:** Implementa o controle **DA03 / RS03** para mitigar fraudes de adulteração (**R04 / Tampering**), recalculando preços de produtos e taxas diretamente a partir da base de dados e serviço de logística antes de qualquer chamada ao Gateway de Pagamento.
5. **Persistência Segura (Banco de Dados):**
   - Acesso via consultas parametrizadas (*Prepared Statements*) prevenindo injeções de SQL.
   - Criptografia em repouso (*at-rest*) para proteção de dados pessoais (LGPD).
6. **Auditoria e Detecção (SIEM):**
   - Todos os componentes emitem logs estruturados para o serviço central de auditoria, alimentando as regras de detecção de intrusões (Etapa 6).
