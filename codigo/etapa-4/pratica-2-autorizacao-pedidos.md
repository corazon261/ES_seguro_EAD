# Prática 2: Autorização Server-Side para Acesso a Pedidos (Mitigação de IDOR)

**Objetivo:** Mitigar o risco **R06 (Information Disclosure - Exposição de dados de terceiros por falha de autorização)**.  
**Responsável:** Lucas Kaue Ribeiro Weber  
**Referência OWASP:** OWASP Top 10:2021 — A01: Broken Access Control / CWE-639  

Esta implementação em pseudocódigo demonstra a aplicação do controle de acesso seguro no lado do servidor (*Object-Level Authorization*), assegurando que nenhum cliente consiga visualizar detalhes de pedidos pertencentes a outros usuários simplesmente alterando o identificador do pedido na URL ou payload da requisição.

---

## 1. Regra de Negócio e Controle de Segurança

1. **Autenticação Obrigatória:** O endpoint exige um token JWT válido no cabeçalho `Authorization: Bearer <token>`.
2. **Extração Confiável do Usuário:** O identificador do usuário autenticado (`token.usuario_id`) e seu perfil (`token.role`) são extraídos diretamente da assinatura criptográfica do token, e não de parâmetros enviados pelo cliente.
3. **Consulta Parametrizada:** A busca ao pedido no banco de dados utiliza parâmetros preparados contra SQL Injection.
4. **Verificação de Propriedade do Objeto (Anti-IDOR):** O backend valida se `pedido.cliente_id == usuario_id` ou se o usuário possui perfil especial de administrador (`ADMIN`).
5. **Tratamento de Exceções Seguro:** Tentativas de acesso não autorizado retornam estritamente `HTTP 403 Forbidden` sem vazar detalhes sobre a existência do pedido ou dados de terceiros.
6. **Auditoria e Monitoramento:** Toda tentativa de acesso negado é registrada em log de segurança estruturado para alimentar as regras de detecção de intrusões (SIEM).

---

## 2. Pseudocódigo: Controlador de Consulta de Pedidos (`API de Pedidos`)

```python
// Pseudocódigo: Endpoint GET /api/v1/pedidos/{pedido_id}
funcao obterDetalhesPedido(Requisicao req, String pedido_id):
    
    // 1. Validação de Autenticação (Token JWT no Header)
    token = req.headers.obter("Authorization")
    se token == nulo ou nao TokenJWT.validarAssinatura(token):
        retornar ErroHTTP(401, "Token de autenticação ausente ou inválido.")
        
    usuario_autenticado_id = TokenJWT.extrairClaim(token, "usuario_id")
    usuario_role = TokenJWT.extrairClaim(token, "role")
    
    // 2. Validação sintática do identificador do pedido (UUIDv4)
    se nao Validador.isUUID(pedido_id):
        retornar ErroHTTP(400, "Identificador de pedido em formato inválido.")
        
    // 3. Consulta segura ao Banco de Dados (Query Parametrizada)
    pedido = DB.consultar(
        "SELECT id, cliente_id, restaurante_id, status, itens, total, endereco_entrega, criado_em " +
        "FROM Pedidos WHERE id = ?", 
        pedido_id
    )
    
    // 4. Verificação de Existência do Pedido
    se pedido == nulo:
        retornar ErroHTTP(404, "Pedido não encontrado.")
        
    // 5. CONTROLE DE AUTORIZAÇÃO DE NÍVEL DE OBJETO (Mitigação do Risco R06 / IDOR)
    // Verifica se o usuário autenticado é o legítimo dono do pedido ou se é administrador
    se (pedido.cliente_id != usuario_autenticado_id) e (usuario_role != "ADMIN"):
        
        // Registra o evento de tentativa de acesso indevido nos logs de segurança
        Logger.registrar(
            nivel: "ALERTA",
            evento: "Tentativa de acesso não autorizado a pedido de terceiro (Possível IDOR)",
            usuario_solicitante: usuario_autenticado_id,
            pedido_solicitado: pedido_id,
            dono_legitimo_pedido: pedido.cliente_id,
            ip_origem: req.ip,
            user_agent: req.headers.obter("User-Agent"),
            horario: DataHora.agoraUTC()
        )
        
        // Nega o acesso imediatamente com 403 Forbidden sem vazar dados sensíveis
        retornar ErroHTTP(403, "Acesso não autorizado ao recurso solicitado.")
        
    // 6. Autorização bem-sucedida: Retorna os dados do pedido ao proprietário legítimo
    dados_sanitizados = {
        "id": pedido.id,
        "status": pedido.status,
        "itens": pedido.itens,
        "total": pedido.total,
        "endereco_entrega": pedido.endereco_entrega,
        "criado_em": pedido.criado_em
    }
    
    Logger.registrar(
        nivel: "INFO",
        evento: "Consulta de pedido autorizada com sucesso",
        usuario: usuario_autenticado_id,
        pedido_id: pedido.id
    )
    
    retornar SucessoHTTP(200, "Pedido recuperado com sucesso.", dados_sanitizados)
```

---

## 3. Cobertura de Testes

- **Caso Válido (TS03):** Usuário autenticado `USR-001` consulta pedido `PED-100` cujo `cliente_id` é `USR-001` → Retorno `200 OK` com dados completos.
- **Caso Não Autorizado (TS04):** Usuário autenticado `USR-002` tenta consultar pedido `PED-100` cujo `cliente_id` é `USR-001` → Retorno `403 Forbidden`, nenhum dado exposto e log de segurança registrado.
