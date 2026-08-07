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
| R04 | Tampering (T02, CA02) | Um cliente altera o valor/itens do pedido antes do pagamento | Cálculo/validação de preços no lado do cliente; API não revalida valores | 3 | 3 | 9 | **Alto** _(proposto — Gustavo revisa/justifica)_ |
| R05 | Repudiation (T03) | Um entregador/cliente nega ter entregue/recebido o pedido | Ausência de código de confirmação (OTP) e de logs auditáveis com timestamp | 3 | 2 | 6 | **Médio** _(proposto — Gustavo revisa/justifica)_ |
| R06 | Information Disclosure (T04, CA03) | Um usuário acessa pedidos/dados de terceiros trocando o identificador na requisição | Falta de verificação de propriedade do objeto (IDOR / broken access control) | 3 | 4 | 12 | **Crítico** _(proposto — Luiz revisa/justifica)_ |
| R07 | Information Disclosure (T10) | Dados do cliente permanecem acessíveis ao entregador após a entrega | Ausência de mascaramento e de expiração do acesso aos dados | 3 | 3 | 9 | **Alto** _(proposto — Luiz revisa/justifica)_ |
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
| R04 | _(preencher)_ | | | | | |
| R05 | _(preencher)_ | | | | | |
| R06 | _(preencher)_ | | | | | |
| R07 | _(preencher)_ | | | | | |
| R08 | _(preencher)_ | | | | | |

> Analisem cada relação — não marquem todas as funções automaticamente.

## 10. Plano de tratamento

| Risco | Estratégia | Controles propostos | Funções NIST | Responsáveis | Evidências e verificação |
| --- | --- | --- | --- | --- | --- |
| R01 | Reduzir | Autenticação multifator (MFA); limite de tentativas + bloqueio temporário; alerta de login em novo dispositivo | Protect, Detect, Respond | Desenvolvimento e Infra | Testes de login com/sem MFA; logs de tentativas; simulação de conta comprometida |
| R02 | Reduzir/Evitar | Autorização verificada no servidor (RBAC) em todos os endpoints; negar por padrão; testes de autorização por perfil | Protect, Detect, Govern | Desenvolvimento | Testes de acesso a endpoints admin com usuário comum (deve ser negado e registrado) |
| R03 | Reduzir | Código de confirmação de entrega (OTP) informado pelo cliente; checagem de coerência rota/tempo; detecção de *mock location* | Protect, Detect, Respond | Desenvolvimento e Operações | Registro de OTP por entrega; relatório de entregas sem OTP; testes com app de fake GPS |
| R04–R08 | _(preencher)_ | _(controles específicos e verificáveis)_ | _(funções)_ | _(responsáveis)_ | _(evidências)_ |

> Controles devem ser **específicos e observáveis**. Evitem "aumentar a
> segurança", "usar criptografia", "aplicar o NIST" sem dizer onde, como e quem
> verifica.

## 11. Ordem inicial de implementação

_(Responsável: Gustavo.)_ Definir e justificar a ordem de implementação dos
controles, considerando riscos críticos/altos, dependências técnicas, controles
que reduzem vários riscos, custo/complexidade e urgência.

## 12. Estimativa do risco residual

| Risco | Nível inicial | Nível residual esperado | Condição para aceitar o residual |
| --- | --- | --- | --- |
| R01 | Crítico (12) | Médio (≈6) | MFA obrigatório e monitoramento de login ativos e testados |
| R02 | Alto (8) | Baixo–Médio (≈4) | Autorização server-side coberta por testes automatizados |
| R03 | Alto (9) | Médio (≈4–6) | OTP de entrega adotado e detecção de mock location em produção |
| R04–R08 | _(preencher)_ | _(preencher)_ | _(preencher)_ |

> O risco residual é uma **estimativa**: a redução só se confirma após
> implementação, testes e evidências.

## 13. Considerações finais

_(Responsável: Andre.)_ Sintetizar: riscos mais importantes; razões da
priorização; estratégias de tratamento predominantes; funções do NIST mais
relevantes para o RapidoFood; controles essenciais; principais dificuldades;
limitações da avaliação; e pontos a detalhar nas próximas etapas.
