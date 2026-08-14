# Etapa 6 — Monitoramento e Detecção de Intrusões

**Sistema:** RapidoFood (app de delivery)

Esta etapa tem como objetivo demonstrar como o sistema poderia identificar
comportamentos suspeitos depois de entrar em operação.

Não será necessário instalar ou implementar um sistema de detecção de
intrusões. O entregável será um roteiro ou descrição textual.

---

## 1. O que é detecção de intrusões

**Responsável: Andre**

A **detecção de intrusões** consiste no monitoramento contínuo e na análise dos eventos e logs gerados pelo sistema para identificar atividades anômalas, não autorizadas ou maliciosas que busquem violar as políticas de segurança da aplicação.

O **objetivo da detecção** é conceder visibilidade operacional à equipe de segurança, reduzindo o tempo de identificação (*Mean Time to Detect - MTTD*) e de resposta a incidentes. Diferente da prevenção — que busca erguer barreiras antes que o ataque aconteça —, a detecção atua identificando comportamentos suspeitos em tempo de execução ou após a tentativa de exploração.

Para o **RapidoFood**, a detecção de intrusões é fundamental pelos seguintes motivos:
* **Proteção de ativos sensíveis:** A plataforma lida diretamente com dados pessoais de clientes (PII), endereços de entrega, geolocalização de entregadores e transações financeiras com gateways de pagamento.
* **Complementaridade ao princípio de Defesa em Profundidade:** Nenhum controle preventivo é infalível. Mesmo com autenticação forte (MFA) e validações *server-side*, atacantes podem tentar explorar falhas de lógica, realizar ataques de força bruta ou enumerar identificadores.
* **Conformidade com a LGPD e regulamentações:** A detecção rápida de acessos indevidos a dados de terceiros (como falhas de autorização IDOR) permite isolar a ameaça e cumprir com as obrigações de notificação de incidentes aos titulares e à ANPD.

---

## 2. Prevenção x detecção

**Responsável: Renata**

Prevenção e detecção são medidas diferentes, mas complementares.

**Prevenir** significa utilizar controles para evitar ou reduzir a possibilidade
de que um incidente de segurança aconteça. No RapidoFood, exemplos de
prevenção definidos nas etapas anteriores são a autenticação forte e proteção
contra tentativas automatizadas relacionadas ao **R01 — Sequestro de conta**,
a autorização server-side para impedir o acesso a pedidos de terceiros no
**R06 — Exposição de dados de terceiros** e a validação server-side dos valores
das transações no **R04 — Alteração de valores no carrinho e pedido**.

**Detectar** significa identificar eventos ou comportamentos que possam indicar
uma atividade suspeita ou uma tentativa de violação dos controles de segurança.
A detecção não substitui a prevenção: mesmo com controles preventivos, é
necessário registrar e analisar eventos para identificar tentativas de ataque,
comportamentos anormais ou possíveis falhas.

No contexto do RapidoFood, o monitoramento pode complementar os controles das
etapas anteriores. Por exemplo, tentativas repetidas de autenticação podem
indicar uma tentativa relacionada ao R01; acessos negados ou consultas
incomuns a pedidos podem indicar comportamento relacionado ao R06; e
divergências entre valores enviados pelo cliente e os valores recalculados pelo
servidor podem indicar uma tentativa de alteração relacionada ao R04.

Assim, a prevenção busca **impedir ou dificultar o incidente**, enquanto a
detecção busca **identificar sinais de que uma tentativa ou incidente pode
estar acontecendo**, permitindo uma resposta adequada.


---

## 3. Eventos que devem ser registrados

**Responsável: Gustavo**

Descrever quais eventos do RapidoFood deveriam gerar registros para permitir
a identificação de comportamentos suspeitos.

Podem ser considerados, conforme os riscos do projeto:

- tentativas de autenticação;
- alterações de permissões;
- acessos a dados sensíveis;
- alterações de pedidos;
- operações administrativas;
- requisições bloqueadas;
- eventos relacionados a pagamentos;
- outros eventos relevantes.

A escolha deverá ser relacionada aos riscos do sistema.

*(preencher — Gustavo)*

---

## 4. Regra de detecção 1

**Responsável: Lucas**

| Campo | Conteúdo |
|---|---|
| Risco observado | **R01 — Sequestro de conta (Spoofing)** (Crítico, 12), originado da ameaça T01 e do caso de abuso CA01. |
| Fonte de dados | Logs estruturados do Serviço de Autenticação (`Auth Service`) e do API Gateway no endpoint `/api/v1/auth/login`, registrando: timestamp em UTC, IP de origem, User-Agent, identificador da conta (e-mail/CPF informado), resultado da tentativa (`FALHA_SENHA`, `FALHA_MFA`, `SUCESSO`) e tempo de resposta da requisição. |
| Condição de alerta | Disparar alerta de severidade alta quando ocorrerem: <br>1. **5 ou mais tentativas consecutivas de autenticação com falha para a mesma conta em uma janela de 5 minutos** (indicativo de ataque direcionado de força bruta); OU <br>2. **15 ou mais falhas de autenticação originadas do mesmo endereço IP contra contas distintas em uma janela de 10 minutos** (indicativo de ataque distribuído de *credential stuffing* ou enumeração automatizada de credenciais). |
| Resposta inicial | 1. Aplicar bloqueio temporário de 15 minutos para novas tentativas de autenticação originadas pelo IP suspeito e/ou direcionadas à conta-alvo, ativando desafio CAPTCHA e revalidação estrita de MFA no desbloqueio;<br>2. Enviar imediatamente uma notificação transacional de segurança (via e-mail e SMS) ao titular legítimo da conta, alertando sobre a atividade suspeita e orientando redefinição de senha preventiva;<br>3. Revogar tokens JWT de sessões ativas caso tenha havido sucesso em qualquer tentativa anterior recente do mesmo IP;<br>4. Preservar os logs forenses de conexão e abrir incidente automático no SIEM para análise operacional. |

A regra permite identificar proativamente tentativas automatizadas de quebra de autenticação antes que atacantes obtenham sucesso no comprometimento da conta de clientes ou entregadores. Ela complementa a prevenção estabelecida no **DA01 / RS01** (MFA e Rate Limiting).

---

## 5. Regra de detecção 2

**Responsável: Luiz**

| Campo | Conteúdo |
|---|---|
| Risco observado | **R06 — Exposição de dados de terceiros por falha de autorização** (Crítico, 12), originado da ameaça T04 e do caso de abuso CA03. |
| Fonte de dados | Log de acesso da API de pedidos, registrando por requisição: usuário autenticado, identificador do pedido solicitado, dono real do pedido, código de resposta HTTP e horário. |
| Condição de alerta | Disparar quando uma mesma conta receber **5 ou mais respostas 403 em endpoints de pedido em 1 minuto**, ou quando consultar **20 ou mais identificadores distintos de pedido em 5 minutos** — sinal de enumeração sequencial. A segunda condição é necessária porque, enquanto a falha de autorização existir, as consultas indevidas retornam 200 e passariam despercebidas pela primeira. |
| Resposta inicial | Bloquear temporariamente as consultas de pedido daquela conta, preservar os registros para análise e notificar a equipe de segurança. Confirmada a leitura indevida, identificar os titulares afetados e acionar a comunicação prevista na LGPD. |

A regra deverá representar um comportamento suspeito relacionado a um dos
riscos do RapidoFood.

---

## 6. Regra de detecção 3


**Responsável: Renata**

| Campo | Conteúdo |
|---|---|
| Risco observado | **R04 — Alteração de valores no carrinho e pedido (Tampering)** (Alto, 9), originado da ameaça T02 e do caso de abuso CA02. |
| Fonte de dados | Logs do backend/API de pedidos e do processo de checkout, registrando o usuário autenticado, identificador do pedido, valores enviados pelo cliente, valores recalculados pelo servidor, divergências encontradas, código de resposta e horário da requisição. |
| Condição de alerta | Gerar um alerta quando forem identificadas divergências entre os valores enviados pelo cliente e os valores recalculados pelo servidor, especialmente quando ocorrerem tentativas repetidas de alteração de preço unitário, taxa ou valor total em um curto período. |
| Resposta inicial | Rejeitar a transação quando a divergência for identificada, registrar o evento nos logs de segurança e encaminhar o alerta para análise. Caso sejam identificadas tentativas repetidas ou comportamento suspeito, preservar os registros para investigação e aplicar os mecanismos de contenção previstos pelo sistema. |

A regra está relacionada ao **R04**, pois permite identificar tentativas de
adulteração dos valores de uma transação. Ela complementa o controle preventivo
definido na Etapa 4, no qual o backend recalcula e valida os valores antes de
encaminhar a transação ao gateway de pagamento.

---

## 7. O que acontece depois de um alerta

**Responsável: Andre**

A emissão de um alerta por uma regra de detecção marca o início do fluxo de resposta a incidentes do RapidoFood. O processo pós-alerta deve seguir um roteiro padronizado e auditável composto pelas seguintes etapas:

1. **Triagem e Análise Inicial:**
   A equipe de resposta a incidentes (ou o sistema automatizado de SIEM/Monitoramento) recebe o alerta e analisa as fontes de dados associadas (logs da API, histórico do usuário, IP de origem, timestamp e payload). O objetivo é avaliar a gravidade e determinar se o alerta representa um ataque real (**verdadeiro positivo**) ou um comportamento atípico do sistema/usuário legítimo (**falso positivo**).

2. **Confirmação ou Descarte:**
   * *Caso seja Falso Positivo:* O evento é registrado, arquivado e a regra de detecção é ajustada para calibrar a sensibilidade e evitar fadiga de alertas.
   * *Caso seja Verdadeiro Positivo:* Confirma-se a ocorrência do incidente de segurança e ele é classificado pelo nível de severidade (Baixa, Média, Alta ou Crítica).

3. **Contenção Inicial:**
   Execução imediata de medidas automatizadas ou manuais para interromper a ação maliciosa em andamento e conter os danos:
   * Bloqueio temporário do endereço IP ou da conta envolvida na suspeita.
   * Revogação imediata dos tokens de sessão JWT ativos do usuário comprometido.
   * Rejeição automática de transações financeiras com inconsistência de valor.
   * *Atenção:* Os registros de acesso (logs) **nunca** devem ser apagados durante a contenção, garantindo a preservação das evidências para investigação.

4. **Registro do Incidente:**
   Abertura de um chamado/registro de incidente formal contendo horário do disparo, regra violada, ativos afetados, dados expostos, ações de contenção adotadas e a identificação do analista responsável.

5. **Comunicação aos Responsáveis:**
   * Notificação dos times de Desenvolvimento e Infraestrutura/DevSecOps para análise técnica da brecha.
   * Acionamento do Encarregado de Dados (DPO) e do time Jurídico caso o incidente envolva o vazamento de dados pessoais (como no R06), garantindo a conformidade com a LGPD.

6. **Tratamento, Erradicação e Recuperação:**
   * Correção da vulnerabilidade na aplicação (ex: aplicação de um *patch* no endpoint vulnerável).
   * Restauração da operação segura do sistema e desbloqueio das contas atingidas após redefinição de credenciais.
   * Condução de uma reunião de lições aprendidas (*post-mortem*) para aprimorar os controles preventivos e as regras de monitoramento.

---

## 8. Relação com as etapas anteriores

**Responsável: Gustavo**

Relacionar as três regras de detecção com os riscos e controles definidos nas
Etapas 2, 3, 4 e 5, quando aplicável.

| Regra | Risco relacionado | Controle ou medida relacionada |
|---|---|---|
| Regra 1 | *(preencher — Gustavo)* | *(preencher — Gustavo)* |
| Regra 2 | *(preencher — Gustavo)* | *(preencher — Gustavo)* |
| Regra 3 | *(preencher — Gustavo)* | *(preencher — Gustavo)* |

---

## 9. Divisão da Etapa 6

| Integrante | Responsabilidade |
|---|---|
| **Andre** | Conceito de detecção de intrusões + resposta após um alerta |
| **Renata** | Prevenção x detecção + Regra de detecção 3 |
| **Gustavo** | Eventos que devem ser registrados + relação das regras com as etapas anteriores |
| **Lucas** | Regra de detecção 1 |
| **Luiz** | Regra de detecção 2 |

---

## 10. Checklist da etapa

Antes de concluir a etapa, verificar se:

- foi explicado o conceito de detecção de intrusões;
- foi apresentada a diferença entre prevenção e detecção;
- foram identificados eventos que devem ser registrados;
- existem exatamente três regras de detecção;
- cada regra possui risco observado;
- cada regra possui fonte de dados;
- cada regra possui condição de alerta;
- cada regra possui resposta inicial;
- foi explicado o que acontece depois de um alerta;
- as regras estão relacionadas aos riscos do RapidoFood;
- a participação individual está demonstrada por commits.
