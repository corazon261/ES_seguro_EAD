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
| Risco observado | *(preencher — Lucas)* |
| Fonte de dados | *(preencher — Lucas)* |
| Condição de alerta | *(preencher — Lucas)* |
| Resposta inicial | *(preencher — Lucas)* |

A regra deverá representar um comportamento suspeito relacionado a um dos
riscos do RapidoFood.

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

Descrever brevemente o que deveria acontecer depois que uma regra de
detecção gerar um alerta.

Considerar, conforme o cenário:

- análise do alerta;
- confirmação ou descarte do evento;
- contenção inicial, quando necessária;
- registro do incidente;
- comunicação aos responsáveis;
- encaminhamento para tratamento e recuperação.

*(preencher — Andre)*

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
