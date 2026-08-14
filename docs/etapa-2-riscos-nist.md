# Etapa 2 — Análise, Priorização e Tratamento de Riscos com o NIST CSF

Sistema: **RapidoFood** (app de delivery). Esta etapa dá **continuidade à Etapa 1**:
cada risco abaixo tem origem em uma ameaça STRIDE (T0x) ou caso de abuso (CA0x)
já descritos no [Entregável 1](<../Passo 2 (Entregavel 1) - Modelagem de ameaças e Casos de Abuso.md>).

> **Como preencher:** as linhas de risco marcadas com _(proposto — Fulano
> revisa/justifica)_ têm valores sugeridos para dar consistência à tabela; o
> responsável deve confirmar os números e escrever a justificativa. As seções de
> **justificativa**, **plano de tratamento** e **risco residual** de cada risco
> devem ser preenchidas por quem é dono daquele risco (ver
> [plano de trabalho](plano-de-trabalho.md)).

## 1. Critérios de probabilidade

| Valor | Classificação | Critério |
| --- | --- | --- |
| 1 | Baixa | Depende de condições incomuns, acesso muito específico ou grande capacidade técnica |
| 2 | Média-baixa | É possível, mas depende de uma vulnerabilidade ou condição específica |
| 3 | Média-alta | É plausível e pode ocorrer em situações comuns de uso ou ataque |
| 4 | Alta | Pode ocorrer com facilidade, frequência ou durante condições previsíveis do sistema |

## 2. Critérios de impacto

| Valor | Classificação | Critério |
| --- | --- | --- |
| 1 | Baixo | Pequeno transtorno, corrigível rapidamente |
| 2 | Moderado | Interrupção ou inconsistência limitada, com recuperação possível |
| 3 | Alto | Prejuízo relevante aos usuários, ao negócio, à administração ou à privacidade |
| 4 | Muito alto | Afeta muitos usuários, compromete operações críticas ou causa prejuízo grave |

## 3. Cálculo e classificação

**Pontuação = Probabilidade × Impacto**

| Pontuação | Nível do risco |
| --- | --- |
| 1 a 3 | Baixo |
| 4 a 7 | Médio |
| 8 a 11 | Alto |
| 12 a 16 | Crítico |

> A pontuação apoia a comparação, mas não substitui a análise do contexto: dois
> riscos com a mesma nota podem ter prioridades diferentes.

## 4. Registro de riscos

| ID | Origem STRIDE / CA | Evento de risco | Vulnerabilidade ou condição | Prob. | Impacto | Pontuação | Nível |
| --- | --- | --- | --- | :---: | :---: | :---: | --- |
| R01 | Spoofing (T01, CA01) | Um atacante acessa a conta de um usuário e realiza operações em seu nome | Credenciais reutilizadas/vazadas, ausência de MFA e de limite de tentativas de login | 3 | 4 | 12 | **Crítico** |
| R02 | Elevation of Privilege (T08, CA01) | Um usuário comum obtém permissões de administrador | Falha de controle de acesso; autorização não verificada no servidor | 2 | 4 | 8 | **Alto** |
| R03 | Spoofing (T07, CA05) | Um entregador confirma entregas que não realizou falsificando o GPS | Confiança exclusiva na localização do dispositivo; sem confirmação de entrega (OTP) | 3 | 3 | 9 | **Alto** |
| R04 | Tampering (T02, CA02) | Um cliente altera o valor/itens do pedido antes do pagamento | Cálculo/validação de preços no lado do cliente; API não revalida valores | 3 | 3 | 9 | **Alto** |
| R05 | Repudiation (T03) | Um entregador/cliente nega ter entregue/recebido o pedido | Ausência de código de confirmação (OTP) e de logs auditáveis com timestamp | 3 | 2 | 6 | **Médio** |
| R06 | Information Disclosure (T04, CA03) | Um usuário acessa pedidos/dados de terceiros trocando o identificador na requisição | Falta de verificação de propriedade do objeto (IDOR / broken access control) | 3 | 4 | 12 | **Crítico** |
| R07 | Information Disclosure (T10) | Dados do cliente permanecem acessíveis ao entregador após a entrega | Ausência de mascaramento e de expiração do acesso aos dados | 3 | 3 | 9 | **Alto** |
| R08 | Denial of Service (T05, T06, CA04) | Sobrecarga da API/autenticação torna o serviço indisponível | Ausência de rate limiting e de proteção contra volume anômalo de requisições | 2 | 3 | 6 | **Médio** _(proposto — Renata revisa/justifica)_ |

> Riscos opcionais (dos casos extras) que o grupo pode acrescentar: fraude de
> cupons com múltiplas contas (CA06) e manipulação de avaliações (CA08).

## 5. Justificativas

### R01 — Sequestro de conta (Spoofing) — _Lucas_

- **Probabilidade (3):** ataques de *credential stuffing* usando senhas vazadas
  são comuns e automatizáveis; como o login não exige MFA nem limita tentativas,
  o cenário é plausível em uso normal.
- **Impacto (4):** a conta dá acesso a dados pessoais, endereços e ao cartão
  salvo, permitindo pedidos fraudulentos; pode afetar muitos usuários caso a
  base de senhas seja testada em massa.
- **Afetados:** clientes (dados e dinheiro), plataforma (fraude e reputação).
- **Nível (Crítico):** alta plausibilidade combinada a prejuízo financeiro e de
  privacidade justifica a classificação máxima.

### R02 — Escalonamento de privilégio (Elevation) — _Lucas_

- **Probabilidade (2):** depende da existência de uma falha de autorização
  específica; não é trivial, mas é um erro comum em APIs.
- **Impacto (4):** acesso administrativo compromete os dados de todos os
  usuários e a operação da plataforma.
- **Afetados:** todos os usuários e a administração.
- **Nível (Alto):** impacto máximo com probabilidade moderada.

### R03 — Entregas falsas por GPS spoofing (Spoofing) — _Lucas_

- **Probabilidade (3):** aplicativos de *fake GPS* são acessíveis e não exigem
  grande capacidade técnica.
- **Impacto (3):** gera prejuízo financeiro e disputas, mas o efeito é por
  entrega, não sistêmico.
- **Afetados:** clientes (pagam sem receber) e plataforma (repasses e reembolsos).
- **Nível (Alto):** plausível e com prejuízo relevante, ainda que localizado.

### R04 — Alteração de valores no carrinho e pedido (Tampering) — _Gustavo_

- **Probabilidade (3):** ferramentas de interceptação de requisições HTTP/HTTPS (como Burp Suite) são amplamente documentadas e acessíveis; como a requisição parte do cliente, a tentativa de adulterar parâmetros de preços ou taxas no payload é plausível no uso comum.
- **Impacto (3):** a alteração de preços unitários ou zeramento de taxas de entrega gera prejuízo financeiro direto aos restaurantes e à plataforma, além de causar inconsistências contábeis nas transações.
- **Afetados:** restaurantes parceiros e a plataforma (prejuízo financeiro direto e quebra de integridade das transações).
- **Nível (Alto):** a facilidade de interceptação combinada ao prejuízo financeiro direto justifica a classificação de nível Alto (3 × 3 = 9).

### R05 — Contestação indevida de entrega/recebimento (Repudiation) — _Gustavo_

- **Probabilidade (3):** sem validação em duas pontas, é frequente que clientes declarem não ter recebido pedidos para obter reembolso ou que entregadores marquem pedidos como entregues sem concluí-los.
- **Impacto (2):** causa atritos operacionais, desgaste de confiabilidade e custos de reembolso/repasse, mas com prejuízo financeiro restrito a transações individuais (não afeta a infraestrutura nem dados de terceiros).
- **Afetados:** clientes, entregadores e o suporte operacional da plataforma.
- **Nível (Médio):** ocorrência comum em aplicativos de delivery, porém com impacto pontual por evento, resultando em nível Médio (3 × 2 = 6).

### R06 — Exposição de dados de terceiros por falha de autorização (Information Disclosure) — _Luiz_

- **Probabilidade (3):** a exploração não exige ferramenta sofisticada nem
  privilégio especial — basta um cliente legítimo observar o identificador do
  próprio pedido na requisição e substituí-lo por outro. Falhas de autorização a
  nível de objeto estão entre as mais frequentes em APIs REST, e o uso de
  identificadores sequenciais torna a enumeração trivial. Não recebe 4 porque
  ainda depende de o atacante inspecionar o tráfego da aplicação, um passo
  técnico que o usuário comum não executa por acaso.
- **Impacto (4):** a falha não expõe um registro isolado, e sim toda a base de
  pedidos — nome, telefone, endereço de entrega, itens e valores de qualquer
  cliente. Por ser automatizável, atinge muitos usuários de uma só vez e
  configura tratamento irregular de dados pessoais perante a LGPD, com risco de
  sanção da ANPD. A exposição é irreversível: uma vez copiados, os dados não
  podem ser recuperados.
- **Afetados:** todos os clientes da plataforma (dados pessoais e endereços
  residenciais), além da própria empresa nas esferas jurídica e reputacional.
- **Nível (Crítico):** 3 × 4 = 12. A combinação de exploração simples com
  exposição em massa de dados pessoais justifica a classificação máxima, no mesmo
  patamar do R01. Origem: ameaça T04 e caso de abuso CA03.

### R07 — Acesso persistente do entregador aos dados do cliente (Information Disclosure) — _Luiz_

- **Probabilidade (3):** aqui não há ataque a ser executado — a exposição é o
  comportamento padrão do sistema, já que o aplicativo do entregador mantém nome,
  telefone e endereço visíveis após a conclusão da entrega. O que depende de
  intenção é o uso indevido dessa informação, e com uma base grande de
  entregadores esse comportamento é plausível em situações comuns de operação.
- **Impacto (3):** o alcance é menor que o do R06, pois cada entregador só
  enxerga os clientes que atendeu, mas a consequência é grave no plano
  individual: permite contato não solicitado, perseguição e assédio, atingindo a
  segurança física da pessoa. Há também violação do princípio da finalidade
  previsto na LGPD, já que o dado permanece acessível depois de cumprido o
  propósito que justificou a coleta.
- **Afetados:** clientes atendidos por cada entregador, com risco maior para
  pessoas em situação de vulnerabilidade; e a plataforma, responsável legal pelo
  tratamento dos dados.
- **Nível (Alto):** 3 × 3 = 9. Não chega a Crítico porque não permite coleta em
  massa como o R06, mas a gravidade individual e a natureza contínua da exposição
  impedem classificação menor. Origem: ameaça T10.

### R08 — Sobrecarga da API/autenticação (Denial of Service) — *Renata*

- **Probabilidade (2):** o cenário é possível, pois um atacante pode enviar um volume anormal de requisições para a API ou realizar muitas tentativas de login simultâneas. Porém, o ataque depende de volume suficiente para sobrecarregar os recursos do sistema.
- **Impacto (3):** a indisponibilidade da API ou do serviço de autenticação pode impedir clientes, restaurantes e entregadores de utilizar a plataforma normalmente, causando prejuízo relevante à operação.
- **Afetados:** clientes, restaurantes, entregadores e a própria plataforma.
- **Nível (Médio):** a pontuação é 2 × 3 = 6, classificando o risco como Médio.

  ###  R04 — _(a preencher pelos responsáveis)_

> Cada dono deve seguir o modelo acima: justificar probabilidade, impacto, quem é
> afetado e por que o nível representa o contexto.

## 6. Priorização

Ordem inicial sugerida (a confirmar pelo grupo — coordenação: Lucas):

1. **R01 (Crítico, 12)** — afeta muitos usuários, dinheiro e privacidade; base para outros ataques.
2. **R06 (Crítico, 12)** — exposição de dados de terceiros em massa (LGPD).
3. **R03 (Alto, 9)** e **R04 (Alto, 9)** e **R07 (Alto, 9)** — prejuízo financeiro/privacidade relevante.
4. **R02 (Alto, 8)** — impacto máximo, porém probabilidade menor.
5. **R05 (Médio, 6)** e **R08 (Médio, 6)** — importantes, mas com nota menor.

> A priorização considera nota, gravidade, número de afetados, importância do
> ativo e dependências. Justifiquem por que um risco é tratado antes de outro.

## 7. Estratégias de tratamento

Para cada risco, escolher uma estratégia principal:

| Estratégia | Descrição |
| --- | --- |
| Evitar | Eliminar a atividade/condição que dá origem ao risco |
| Reduzir | Medidas para diminuir a probabilidade ou o impacto |
| Compartilhar | Atribuir parte da operação/consequências a um terceiro |
| Aceitar | Manter o risco conscientemente, com justificativa e acompanhamento |

## 8. Funções do NIST CSF 2.0

| Função | Finalidade |
| --- | --- |
| **Govern** | Definir políticas, responsabilidades, prioridades e critérios de decisão |
| **Identify** | Conhecer ativos, dependências, vulnerabilidades e riscos |
| **Protect** | Implementar salvaguardas para reduzir probabilidade ou impacto |
| **Detect** | Identificar eventos suspeitos, falhas e possíveis incidentes |
| **Respond** | Conter, analisar, comunicar e tratar incidentes |
| **Recover** | Restaurar serviços e dados e reduzir os prejuízos |

> Função ≠ controle. Ex.: *Protect* é a função; "proteger o acesso às contas" é
> o resultado esperado; "autenticação multifator" é o controle concreto.

## 9. Mapeamento dos riscos para o NIST CSF

| Risco | Govern | Identify | Protect | Detect | Respond | Recover |
| --- | :---: | :---: | :---: | :---: | :---: | :---: |
| R01 | X | X | X | X | X |  |
| R02 | X |  | X | X |  |  |
| R03 |  | X | X | X | X |  |
| R04 | X | X | X | X | X |  |
| R05 | X |  | X | X | X | X |
| R06 | X | X | X | X | X |  |
| R07 | X | X | X |  |  |  |
| R08   |       X       |     X    |    X    |    X   |    X    |         |

**Justificativa do mapeamento do R06 e do R07** (_Luiz_):

- **R06** — *Govern*: exige política de autorização obrigatória para todo acesso
  a dados de pedidos. *Identify*: depende de inventariar quais endpoints
  devolvem dados pessoais. *Protect*: é onde entra a verificação de propriedade
  do objeto no servidor. *Detect*: a enumeração de identificadores gera um padrão
  de acesso anômalo que pode ser monitorado. *Respond*: um vazamento confirmado
  exige comunicação aos titulares e à ANPD, conforme a LGPD. **Não marca
  *Recover*** porque dado pessoal exposto não se recupera — uma vez copiado, não
  há restauração possível, apenas contenção.
- **R07** — *Govern*: define por quanto tempo o entregador pode ver os dados e
  qual a finalidade. *Identify*: mapeia quais campos o aplicativo do entregador
  realmente precisa. *Protect*: mascaramento e expiração do acesso. **Não marca
  *Detect*, *Respond* nem *Recover***: o problema não é um evento a ser detectado,
  e sim uma característica permanente do sistema — enquanto o acesso não expirar,
  não há incidente a identificar, responder ou recuperar. A correção é de
  projeto, não de monitoramento.

> Analisem cada relação — não marquem todas as funções automaticamente.

## 10. Plano de tratamento

| Risco | Estratégia | Controles propostos | Funções NIST | Responsáveis | Evidências e verificação |
| --- | --- | --- | --- | --- | --- |
| R01 | Reduzir | Autenticação multifator (MFA); limite de tentativas + bloqueio temporário; alerta de login em novo dispositivo | Protect, Detect, Respond | Desenvolvimento e Infra | Testes de login com/sem MFA; logs de tentativas; simulação de conta comprometida |
| R02 | Reduzir/Evitar | Autorização verificada no servidor (RBAC) em todos os endpoints; negar por padrão; testes de autorização por perfil | Protect, Detect, Govern | Desenvolvimento | Testes de acesso a endpoints admin com usuário comum (deve ser negado e registrado) |
| R03 | Reduzir | Código de confirmação de entrega (OTP) informado pelo cliente; checagem de coerência rota/tempo; detecção de *mock location* | Protect, Detect, Respond | Desenvolvimento e Operações | Registro de OTP por entrega; relatório de entregas sem OTP; testes com app de fake GPS |
| R04 | Reduzir / Evitar | Recálculo e validação obrigatória de todos os itens, preços e taxas no backend (`API Gateway`/`Pedidos`) antes de enviar ao gateway de pagamento; rejeição de requisições com divergência de valores. | Protect, Detect, Respond, Govern, Identify | Desenvolvimento e Backend | Testes automatizados de API enviando payloads com `preco_unitario` alterado (a API deve retornar erro HTTP `400 Bad Request`); logs auditáveis de bloqueio. |
| R05 | Reduzir | Geração de código de validação único (OTP) no app do cliente a ser digitado pelo entregador no ato da entrega; criação de logs de transação imutáveis com *timestamp* e geolocalização. | Protect, Detect, Respond, Recover, Govern | Desenvolvimento e Operações | Verificação no banco de dados da obrigatoriedade do campo `otp_verified = true` para alteração de status do pedido para `CONCLUIDO`; relatórios de suporte. |
| R06 | Reduzir | Verificação de propriedade do objeto no servidor em todo endpoint que devolve pedido (comparar o dono do pedido com o usuário do token, negando por padrão); substituição do identificador sequencial por UUID; limite de consultas de pedido por conta por minuto; alerta quando uma conta consulta pedidos que não são dela | Govern, Identify, Protect, Detect, Respond | Desenvolvimento (backend) e Segurança | Teste automatizado que consulta o pedido de outro usuário e exige resposta `403 Forbidden`; revisão de que nenhum endpoint de pedido aceita ID sem checagem de dono; log das tentativas negadas; simulação de enumeração sequencial disparando o alerta |
| R07 | Reduzir | Mascaramento do telefone do cliente (chamada e chat via número intermediário da plataforma); exibição do endereço completo apenas entre o aceite e a confirmação da entrega; revogação automática do acesso aos dados assim que o pedido é concluído, mantendo no histórico apenas data, valor e região | Govern, Identify, Protect | Desenvolvimento (aplicativo do entregador) e Jurídico/DPO | Consulta ao histórico do entregador após a entrega deve retornar os dados mascarados; teste de revogação verificando que a API nega os dados completos para pedido concluído; conferência de que o número real do cliente não trafega para o dispositivo do entregador |
| R08 | Reduzir | Rate limiting por IP e por conta; bloqueio temporário após volume anormal de requisições; monitoramento e alertas de tráfego | Protect, Detect, Respond | Desenvolvimento e Operações | Testes de limite de requisições; registro de bloqueios; verificação dos alertas de tráfego |
| R04 | _(preencher)_ | _(controles específicos e verificáveis)_ | _(funções)_ | _(responsáveis)_ | _(evidências)_ |

> Controles devem ser **específicos e observáveis**. Evitem "aumentar a
> segurança", "usar criptografia", "aplicar o NIST" sem dizer onde, como e quem
> verifica.

## 11. Ordem inicial de implementação

A definição da sequência de implementação dos controles do **RapidoFood** foi estruturada priorizando o nível de risco (Crítico e Alto), a facilidade/custo de implementação técnica e o bloqueio de vulnerabilidades que servem de porta de entrada para ataques encadeados:

1. **1º — R04 (Validação Server-Side de Preços no Backend):**
   - *Justificativa:* Apresenta urgência financeira e baixo custo/complexidade de implementação técnica no servidor backend. Validar os preços no servidor elimina imediatamente fraudes diretas de adulteração no carrinho sem depender da alteração de fluxos operacionais de rua ou regras complexas de interface.
2. **2º — R01 (Autenticação Forte - MFA e Rate Limiting no Login):**
   - *Justificativa:* Risco Crítico (Nota 12). Mitiga o sequestro de contas e o *credential stuffing*, protegendo a base de clientes e seus dados sensíveis (endereços e cartões salvos), servindo de base de segurança para toda a plataforma.
3. **3º — R06 (Controle de Acesso a Nível de Objeto - IDOR):**
   - *Justificativa:* Risco Crítico (Nota 12). A implementação de verificações de autorização *server-side* em endpoints de pedidos previne vazamentos em massa de PII (dados pessoais) e sanções graves da LGPD.
4. **4º — R02 (Controle de Acesso Baseado em Perfis - RBAC):**
   - *Justificativa:* Risco Alto (Nota 8). Evita que usuários comuns escalem privilégios para perfis administrativos e acessem recursos restritos no sistema.
5. **5º — R03 e R05 (Validação de Entrega via OTP e Logs Auditáveis com Timestamp):**
   - *Justificativa:* Riscos Alto e Médio. A adoção de código OTP no momento da entrega reduz simultaneamente o risco de GPS Spoofing (R03) e o repúdio de entregas (R05), dependendo de atualização simultânea nas aplicações dos clientes e entregadores.
6. **6º — R07 e R08 (Mascaramento de Dados Pós-Entrega e Rate Limiting Geral):**
   - *Justificativa:* Trata a exposição residual de dados do cliente após a conclusão da entrega (R07) e garante a resiliência e estabilidade da API contra picos ou investidas de negação de serviço (R08).

## 12. Estimativa do risco residual

| Risco | Nível inicial | Nível residual esperado | Condição para aceitar o residual |
| ----- | ------------- | ----------------------- | -------------------------------- |
| R01   | Crítico (12)  | Médio (≈6)              | MFA obrigatório e monitoramento de login ativos e testados |
| R02   | Alto (8)      | Baixo–Médio (≈4)        | Autorização server-side coberta por testes automatizados |
| R03   | Alto (9)      | Médio (≈4–6)            | OTP de entrega adotado e detecção de mock location em produção |
| R04   | *(preencher)* | *(preencher)*           | *(preencher)* |
| R05   | Médio (6)     | Baixo (≈3)              | Código de confirmação (OTP), logs de auditoria com timestamp e validação da entrega ativos e testados |
| R06   | Crítico (12)  | Médio (≈6)              | Autorização server-side por objeto, testes contra IDOR e monitoramento de acessos indevidos ativos e testados |
| R07   | Alto (9)      | Médio (≈4–6)            | Mascaramento de dados, expiração do acesso após a entrega e testes de revogação ativos e testados |
| R08   | Médio (6)     | Baixo (≈3)              | Rate limiting, bloqueio de requisições anormais e monitoramento de tráfego ativos e testados |
> O risco residual é uma **estimativa**: a redução só se confirma após
> implementação, testes e evidências.

## 13. Considerações finais

Após a análise e priorização baseada no framework NIST CSF, concluímos que os riscos mais importantes para o sistema RapidoFood são o R01 (Sequestro de contas e acessos indevidos) e o R06 (Acesso a dados de outros usuários via IDOR), ambos classificados como Críticos (nível 12), seguidos pelos riscos ligados à manipulação financeira e de pedidos (R02, R03 e R04), classificados como Altos.

As razões que determinaram essa priorização estão fundamentadas no impacto direto aos ativos de maior valor da plataforma: credenciais, dados financeiros (cartões/gateways) e informações pessoais (endereços e localização). A violação desses ativos resulta em severos prejuízos financeiros e sanções legais (como LGPD), justificando a alta prioridade.

A estratégia de tratamento predominante adotada pelo grupo foi a de Mitigação, buscando reduzir a probabilidade e o impacto das ameaças através da implementação de controles técnicos. Nesse contexto, as funções do NIST CSF mais relevantes para o nosso sistema foram a função PROTEGER (PR), com foco no controle de identidade, autenticação (PR.AA) e segurança de dados (PR.DS), seguida pela função DETECTAR (DE), essencial para identificar anomalias nos logs e transações.

Os controles considerados essenciais para a operação segura do RapidoFood incluem:

1. **Implementação de Autenticação Multifator (MFA).**

2. **Validação obrigatória de autorização e recálculo de valores estritamente no lado do servidor (server-side).**

3. **Uso de códigos de confirmação descartáveis (OTP) para validar a entrega dos pedidos.**

As principais dificuldades encontradas pelo grupo durante esta etapa envolveram a diferenciação exata entre as funções do NIST (especialmente separar os resultados esperados dos controles técnicos aplicados) e a quantificação objetiva dos riscos qualitativos. A principal limitação da avaliação é que a estimativa do risco residual foi feita de forma teórica; em um cenário real, esses valores só poderiam ser validados após testes práticos em ambiente de produção.
