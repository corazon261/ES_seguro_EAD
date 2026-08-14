# Etapa 3 — Projeto de uma Arquitetura Segura

**Sistema:** RapidoFood (app de delivery)

Esta etapa dá continuidade aos riscos e controles definidos na Etapa 2.
O objetivo é transformar os riscos prioritários em requisitos de segurança,
vulnerabilidades catalogadas, uma arquitetura segura e decisões de arquitetura.

---

## 1. Requisitos de segurança

**Responsável principal: Renata**

Os requisitos de segurança abaixo foram derivados dos riscos críticos e altos identificados na Etapa 2. Foram selecionados os riscos **R01, R06 e R04**, mantendo a relação com os controles de segurança definidos anteriormente.

### RS01 — Proteção contra sequestro de contas

| Campo | Conteúdo |
|---|---|
| Risco de origem | **R01 — Sequestro de conta (Spoofing)**, pontuação 12, nível Crítico |
| Requisito de segurança | O RapidoFood deve exigir autenticação forte para acesso às contas e implementar mecanismos de proteção contra tentativas automatizadas de acesso, incluindo MFA, limite de tentativas e bloqueio temporário. |
| Critério de verificação | Deve ser possível verificar que o MFA está habilitado para as contas protegidas e que tentativas sucessivas de autenticação inválida acionam o mecanismo de limitação ou bloqueio. Também deve existir registro das tentativas de autenticação relevantes. |

### RS02 — Controle de acesso aos pedidos

| Campo | Conteúdo |
|---|---|
| Risco de origem | **R06 — Exposição de dados de terceiros por falha de autorização (Information Disclosure)**, pontuação 12, nível Crítico |
| Requisito de segurança | O backend do RapidoFood deve verificar, em todos os endpoints que retornam dados de pedidos, se o usuário autenticado possui autorização para acessar o objeto solicitado, negando o acesso quando não houver propriedade ou permissão correspondente. |
| Critério de verificação | Um usuário autenticado que tentar consultar o identificador de um pedido pertencente a outro usuário deve receber uma resposta de acesso negado, como **HTTP 403 Forbidden**, e a tentativa deve ser registrada para monitoramento. |

### RS03 — Validação dos valores das transações no servidor

| Campo | Conteúdo |
|---|---|
| Risco de origem | **R04 — Alteração de valores no carrinho e pedido (Tampering)**, pontuação 9, nível Alto |
| Requisito de segurança | O backend do RapidoFood deve recalcular e validar os itens, preços e taxas do pedido antes do envio ao gateway de pagamento, sem confiar nos valores enviados pelo cliente. |
| Critério de verificação | Uma requisição contendo alteração indevida de `preco_unitario`, taxa ou outro valor da transação deve ser rejeitada pelo backend, preferencialmente com resposta **HTTP 400 Bad Request**, e a tentativa de divergência deve ser registrada em log. |

---

## 2. Vulnerabilidades catalogadas

**Responsável principal: Lucas**

Para cada requisito, foi identificada uma vulnerabilidade correspondente em catálogos e padrões reconhecidos da indústria (CWE, OWASP Top 10 e OWASP Cheat Sheets), estabelecendo a relação direta com a estrutura do RapidoFood.

| Risco | Vulnerabilidade ou categoria | Referência utilizada | Relação com o sistema |
|---|---|---|---|
| **R01 (RS01)** — Sequestro de conta (Spoofing) | Falha de Identificação e Autenticação / Ausência de Limitação de Tentativas (`CWE-307: Improper Restriction of Excessive Authentication Attempts` e `CWE-287: Improper Authentication`) | **OWASP Top 10:2021 — A07: Identification and Authentication Failures** / OWASP Authentication Cheat Sheet | O endpoint `/api/v1/auth/login` do RapidoFood, ao permitir tentativas sucessivas sem MFA nem *rate limiting*, torna-se vulnerável a ataques de força bruta e *credential stuffing*, permitindo que atacantes assumam contas de clientes e realizem pedidos com cartões salvos. |
| **R06 (RS02)** — Exposição de dados de terceiros (Information Disclosure) | Quebra de Controle de Acesso / Referência Direta Insegura a Objetos (`CWE-639: Authorization Bypass Through User-Controlled Key` / IDOR) | **OWASP Top 10:2021 — A01: Broken Access Control** / OWASP Authorization Cheat Sheet | O backend do RapidoFood, ao receber requisições em `/api/v1/pedidos/{id}` confiando apenas no ID fornecido na URL sem verificar se o `usuario_id` do token JWT autenticado é o dono do pedido, permite a qualquer usuário ler pedidos e dados sensíveis de terceiros (violação LGPD). |
| **R04 (RS03)** — Alteração de valores no carrinho e pedido (Tampering) | Manipulação de Parâmetros no Lado do Cliente / Validação Inadequada de Entradas (`CWE-472: External Control of Assumed-Immutable Web Parameter` e `CWE-20: Improper Input Validation`) | **OWASP Top 10:2021 — A04: Insecure Design** / OWASP Input Validation Cheat Sheet | A aplicação confiava nos valores (`preco_unitario`, `taxa_entrega`, `valor_total`) enviados no payload pelo aplicativo cliente. Um invasor interceptando o tráfego via proxy HTTP pode adulterar preços antes do envio ao gateway de pagamento. |

---

## 3. Diagrama da arquitetura segura

**Responsável principal: Lucas**

O diagrama apresenta a visão estrutural e os controles de segurança do RapidoFood, incluindo:

- Usuários (Cliente, Restaurante, Entregador, Administrador);
- Interfaces (App Mobile, Painel Web SPA);
- Borda e Gateway de API com WAF e Rate Limiting;
- Serviço de Autenticação com mecanismo MFA (OTP) e gerenciador de sessão JWT;
- Serviço de Pedidos com Módulo de Autorização de Objeto (Anti-IDOR) e Validação Server-Side de Preços;
- Banco de dados relacional isolado (PostgreSQL) com consultas parametrizadas e criptografia at-rest;
- Auditoria centralizada e SIEM para monitoramento e detecção de intrusões;
- Integrações externas (Gateway de Pagamento, Provedor de Notificações/SMS, Mapas);
- Posição dos principais controles mitigadores dos riscos R01, R04 e R06.

### Arquivos

Os arquivos estão armazenados em:

`diagramas/etapa-3/`

- **Fonte do diagrama (Mermaid):** [`diagramas/etapa-3/arquitetura-segura.md`](../diagramas/etapa-3/arquitetura-segura.md)
- **Imagem do diagrama (SVG):** [`diagramas/etapa-3/arquitetura-segura.svg`](../diagramas/etapa-3/arquitetura-segura.svg)

---

## 4. Decisões de arquitetura

**Responsável principal: Andre**

Cada decisão deverá apresentar o problema ou risco tratado, a decisão,
o motivo, o componente afetado e o resultado esperado.

### DA01 — Implementação de MFA e Rate Limiting no Login

| Campo | Conteúdo |
|---|---|
| Risco tratado | R01 — Sequestro de conta (Spoofing) |
| Problema | A autenticação atual depende de um único fator (senha) e não bloqueia testes automatizados. Isso permite que invasores assumam as contas dos usuários através de ataques de *credential stuffing* ou força bruta. |
| Decisão tomada | Implementar a obrigatoriedade de Autenticação Multifator (MFA) baseada em OTP (código por SMS/e-mail ou app autenticador) e configurar *rate limiting* com bloqueio temporário após 5 tentativas falhas de login no mesmo IP/conta. |
| Componente afetado | Serviço de autenticação (`Auth`) e Banco de dados (para registro das tentativas e chaves MFA). |
| Justificativa | Apenas senhas não oferecem resistência adequada se vazadas em outros serviços. O MFA anula a utilidade de uma senha roubada sozinha, enquanto o *rate limiting* encarece e inviabiliza ataques de força bruta, atendendo ao RS01. |
| Resultado esperado | Bloqueio eficaz contra robôs que testam senhas em massa, resultando em mitigação severa da probabilidade de invasões de conta sem impacto dramático na usabilidade geral do cliente. |

### DA02 — Validação de Propriedade do Objeto (Mitigação de IDOR)

| Campo | Conteúdo |
|---|---|
| Risco tratado | R06 — Exposição de dados de terceiros por falha de autorização (Information Disclosure) |
| Problema | A API aceita requisições aos detalhes do pedido usando apenas o ID na URL (ex: `/pedidos/123`), confiando que o usuário cliente só acessará seus próprios pedidos. Isso permite que qualquer usuário logado mude o ID e veja dados de terceiros. |
| Decisão tomada | Implementar autorização de nível de objeto (server-side) verificando sempre se o `user_id` vinculado ao token JWT (sessão) corresponde ao `dono_id` do pedido no banco de dados. Além disso, migrar os identificadores sequenciais para UUIDs aleatórios. |
| Componente afetado | Aplicação / API (Controladores de Pedido) e Banco de dados. |
| Justificativa | Adotar o princípio do privilégio mínimo e a premissa do modelo *Zero Trust*, em que a autorização deve ser revalidada a cada requisição para os recursos, impedindo enumeração (UUID) e acesso indevido (RS02). |
| Resultado esperado | Qualquer requisição a um pedido do qual o usuário não é o dono originará um erro imediato de permissão (`HTTP 403 Forbidden`), impedindo integralmente o vazamento em massa de dados protegidos pela LGPD. |

### DA03 — Recálculo Obrigatório de Valores no Servidor

| Campo | Conteúdo |
|---|---|
| Risco tratado | R04 — Alteração de valores no carrinho e pedido (Tampering) |
| Problema | O sistema aceita a requisição de fechamento de carrinho e pagamento confiando nos valores que o aplicativo cliente (app) envia. Um invasor usando um proxy pode interceptar o payload e alterar `preco_unitario` para R$0,01. |
| Decisão tomada | Todo recálculo financeiro e validação de valores deverá ocorrer estritamente no backend (server-side). A API usará apenas os IDs dos produtos e a quantidade, consultando o banco de dados para montar o valor total e enviar ao gateway. |
| Componente afetado | Aplicação / API, Integração com o Gateway de Pagamento e Banco de dados. |
| Justificativa | Uma das regras máximas de segurança de software é nunca confiar em dados advindos do lado do cliente (Client-Side). O processamento no backend garante a integridade matemática da transação e impede fraudes, conforme exigido no RS03. |
| Resultado esperado | Tentativas de enviar um payload manipulado ao sistema retornarão erro `HTTP 400 Bad Request` devido à divergência de cálculo. A fraude será bloqueada antes que qualquer solicitação chegue ao gateway de pagamento. |

---

## 5. Revisão e coerência com a Etapa 2

**Responsável: Renata**

Os requisitos de segurança foram revisados em relação aos riscos identificados na Etapa 2.

- **RS01** foi derivado do **R01 — Sequestro de conta (Spoofing)**, classificado como risco crítico.
- **RS02** foi derivado do **R06 — Exposição de dados de terceiros por falha de autorização (Information Disclosure)**, classificado como risco crítico.
- **RS03** foi derivado do **R04 — Alteração de valores no carrinho e pedido (Tampering)**, classificado como risco alto.

Os requisitos permanecem coerentes com os riscos de origem e com os controles de segurança definidos anteriormente. As medidas propostas também mantêm a mesma finalidade dos controles da Etapa 2, sem alterar os riscos originalmente identificados.

A revisão não identificou conflitos entre os requisitos desta etapa e os riscos utilizados como base na Etapa 2.

---

## 6. Revisão das referências

**Responsável:** Gustavo**

A validação das referências técnicas utilizadas na catalogação das vulnerabilidades (Seção 2) confirma a precisão dos mapeamentos para o contexto do **RapidoFood**:

1. **Validação do R01 (CWE-307 / OWASP A07:2021):** O enquadramento em *Identification and Authentication Failures* e *CWE-307* é preciso, pois descreve diretamente a ausência de limitação de tentativas e a falta do fator duplo de autenticação na rota de login (`/api/v1/auth/login`), técnica primária explorada em ataques de *credential stuffing*.
2. **Validação do R06 (CWE-639 / OWASP A01:2021):** A associação com *Broken Access Control* e *CWE-639 (IDOR)* reflete adequadamente a falha no endpoint `/api/v1/pedidos/{id}`, em que a aplicação falha ao não relacionar a chave primária do recurso ao sujeito autenticado no token JWT.
3. **Validação do R04 (CWE-472 / OWASP A04:2021):** O mapeamento em *Insecure Design* e *CWE-472* é correto, pois conceitua a falha de arquitetura em confiar no envio de parâmetros imutáveis (como preços unitários e taxas) a partir do cliente sem revalidação *server-side*.
4. **Adequação dos Cheat Sheets da OWASP:** As referências aos manuais práticos da OWASP (*Authentication*, *Authorization* e *Input Validation*) fornecem diretrizes consolidadas e reconhecidas para orientar as práticas de implementação segura das etapas seguintes.

---

## 7. Revisão final da arquitetura

**Responsável:** Gustavo**

Após a análise integrada dos artefatos desenvolvidos nesta Etapa 3, confirma-se a consistência, a coerência e a viabilidade da arquitetura de segurança proposta para o **RapidoFood**:

1. **Alinhamento dos Requisitos e Decisões de Arquitetura:** As Decisões de Arquitetura (DA01 a DA03) e os Requisitos de Segurança (RS01 a RS03) tratam diretamente os riscos de maior prioridade (Críticos e Altos) herdados da Etapa 2 (R01, R06 e R04), garantindo cobertura contra sequestro de contas, vazamento de PII e adulteração de valores.
2. **Coerência com o Diagrama de Arquitetura:** O diagrama contempla todos os componentes essenciais da solução (Interfaces, API Gateway/WAF, Serviço de Auth, Módulo de Autorização Anti-IDOR, Recálculo Server-Side, Banco de Dados isolado e SIEM/Auditoria), posicionando adequadamente os pontos de verificação e os controles mitigadores.
3. **Compatibilidade Operacional:** Os controles definidos garantem o cumprimento dos requisitos legais (LGPD) e mitigam riscos de fraude financeira sem comprometer o fluxo e o desempenho das transações no ecossistema do RapidoFood.
4. **Prontidão do Documento:** A estrutura foi completamente revisada, os mapeamentos da OWASP/CWE foram auditados e a arquitetura está formalmente validada para embasar as próximas etapas de implementação e testes de segurança.

---

## Referências

- MITRE. **CWE-307: Improper Restriction of Excessive Authentication Attempts**. Common Weakness Enumeration.
- MITRE. **CWE-639: Authorization Bypass Through User-Controlled Key**. Common Weakness Enumeration.
- MITRE. **CWE-472: External Control of Assumed-Immutable Web Parameter**. Common Weakness Enumeration.
- OWASP. **OWASP Top 10:2021 — A01: Broken Access Control**.
- OWASP. **OWASP Top 10:2021 — A04: Insecure Design**.
- OWASP. **OWASP Top 10:2021 — A07: Identification and Authentication Failures**.
- OWASP. **Authentication Cheat Sheet**. OWASP Cheat Sheet Series.
- OWASP. **Authorization Cheat Sheet**. OWASP Cheat Sheet Series.
- OWASP. **Input Validation Cheat Sheet**. OWASP Cheat Sheet Series.
