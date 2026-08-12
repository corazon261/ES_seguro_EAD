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

Para cada requisito, deverá ser identificada uma vulnerabilidade relacionada
em referências reconhecidas, como CWE, OWASP Top 10, OWASP ASVS ou OWASP
Cheat Sheet Series.

| Risco | Vulnerabilidade ou categoria | Referência utilizada | Relação com o sistema |
|---|---|---|---|
| *(preencher — Lucas)* | | | |
| *(preencher — Lucas)* | | | |
| *(preencher — Lucas)* | | | |

---

## 3. Diagrama da arquitetura segura

**Responsável principal: Lucas**

O diagrama deverá apresentar:

- usuários;
- interface ou aplicação;
- serviço de autenticação;
- regras de autorização;
- banco de dados;
- logs ou monitoramento;
- serviços externos relevantes;
- posição dos principais controles.

### Arquivos

Os arquivos deverão ser armazenados em:

`diagramas/etapa-3/`

**Fonte do diagrama:** *(preencher — Lucas)*

**Imagem do diagrama:** *(preencher — Lucas)*

---

## 4. Decisões de arquitetura

**Responsável principal: Andre**

Cada decisão deverá apresentar o problema ou risco tratado, a decisão,
o motivo, o componente afetado e o resultado esperado.

### DA01 — *(preencher — Andre)*

| Campo | Conteúdo |
|---|---|
| Risco tratado | *(preencher)* |
| Problema | *(preencher)* |
| Decisão tomada | *(preencher)* |
| Componente afetado | *(preencher)* |
| Justificativa | *(preencher)* |
| Resultado esperado | *(preencher)* |

### DA02 — *(preencher — Andre)*

| Campo | Conteúdo |
|---|---|
| Risco tratado | *(preencher)* |
| Problema | *(preencher)* |
| Decisão tomada | *(preencher)* |
| Componente afetado | *(preencher)* |
| Justificativa | *(preencher)* |
| Resultado esperado | *(preencher)* |

### DA03 — *(preencher — Andre)*

| Campo | Conteúdo |
|---|---|
| Risco tratado | *(preencher)* |
| Problema | *(preencher)* |
| Decisão tomada | *(preencher)* |
| Componente afetado | *(preencher)* |
| Justificativa | *(preencher)* |
| Resultado esperado | *(preencher)* |

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

**Responsável: Luiz**

Verificar se as referências utilizadas nas vulnerabilidades estão
identificadas corretamente e se a relação entre cada vulnerabilidade e o
risco do RapidoFood está explicada.

*(preencher após a parte de vulnerabilidades)*

---

## 7. Revisão final da arquitetura

**Responsável: Gustavo**

Verificar se:

- as três decisões estão relacionadas aos riscos;
- o diagrama contém os principais componentes e controles;
- as decisões são coerentes com os requisitos de segurança;
- a arquitetura proposta é compatível com o sistema RapidoFood;
- o conteúdo está organizado e pronto para a próxima etapa.

*(preencher após todas as partes anteriores)*

---

## Referências

*(preencher conforme as referências utilizadas pelo grupo.)*
