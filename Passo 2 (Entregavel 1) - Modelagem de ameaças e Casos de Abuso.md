# Entregável 1 — Modelagem de ameaças e Casos de Abuso

Sistema: **RapidoFood** (aplicativo de delivery). Ver a identificação e a
descrição no [README.md](README.md) e os usuários/ativos no
[Passo 1](<Passo 1 - Usuários, ativos e pontos de interação.md>).

## 5. Modelagem de ameaças com STRIDE

| ID | Categoria STRIDE | Componente ou ativo | Ameaça identificada | Possível impacto |
| --- | --- | --- | --- | --- |
| T01 | Spoofing | Conta do usuário | Um atacante utiliza credenciais roubadas (vazamento de outro serviço, phishing ou credential stuffing) para acessar a conta de outra pessoa, já que o login não exige múltiplo fator de autenticação. | Acesso a dados pessoais e endereços, realização de pedidos fraudulentos e uso do saldo/cartão salvo da vítima. |
| T02 | Tampering | _(a preencher por Gustavo)_ | _(ameaça de alteração indevida de dados — ex.: alteração do valor ou dos itens de um pedido antes do pagamento)_ | _(preencher)_ |
| T03 | Repudiation | _(a preencher por Gustavo)_ | _(ameaça de negar uma ação — ex.: usuário nega ter feito um pedido/cancelamento e não há logs confiáveis)_ | _(preencher)_ |
| T04 | Information Disclosure | _(a preencher por Luiz)_ | _(exposição indevida de informações — ex.: falha de autorização expõe dados pessoais ou endereços de terceiros)_ | _(preencher)_ |
| T05 | Denial of Service | API de pedidos | Um atacante envia um grande volume de requisições simultâneas para a API responsável pelo processamento de pedidos, consumindo os recursos do servidor e impedindo que novas solicitações legítimas sejam atendidas. | Clientes não conseguem realizar pedidos, restaurantes deixam de receber novas solicitações e ocorre indisponibilidade parcial do serviço. |
| T06 | Denial of Service | Servidor de autenticação | Um atacante realiza milhares de tentativas simultâneas de login, sobrecarregando o servidor de autenticação e impedindo que usuários legítimos acessem suas contas. | Clientes, restaurantes e entregadores ficam impossibilitados de acessar o aplicativo, interrompendo o funcionamento da plataforma. |
| T07 | Spoofing | Localização do entregador | Um entregador mal-intencionado falsifica o sinal de GPS (GPS spoofing) para simular que está em um local ou que concluiu uma entrega sem tê-la realizado. | Confirmação de entregas falsas, prejuízo a clientes e restaurantes e fraude no repasse de valores. |
| T08 | Elevation of Privilege | Controle de acesso da API | Um usuário comum manipula o identificador de papel/perfil na requisição (ex.: campo `role`) ou acessa um endpoint administrativo sem verificação de autorização (IDOR/broken access control), obtendo permissões de administrador. | Acesso ao painel administrativo, aos dados de todos os usuários e à possibilidade de alterar cadastros e pedidos. |
| T09 | Elevation of Privilege | Perfil de entregador/restaurante | Um entregador ou restaurante explora falhas de autorização para executar ações restritas a outro perfil (ex.: cancelar pedidos de terceiros ou editar cardápios de outra loja). | Manipulação indevida de pedidos e cardápios, prejuízo financeiro e quebra da separação de responsabilidades entre perfis. |

> **Dica:** espera-se pelo menos uma ameaça por categoria. Todas as seis
> categorias do STRIDE devem ser cobertas ao final.

### 5.1 Interpretação da análise

As ameaças mostram que diferentes partes do RapidoFood precisam ser protegidas.
As **contas e credenciais** estão relacionadas à identidade dos usuários
(Spoofing); os **pedidos e valores** dependem da integridade dos dados
(Tampering); os **logs** permitem responsabilizar os autores das operações
(Repudiation); os **dados pessoais, de pagamento e de localização** exigem
confidencialidade (Information Disclosure); o serviço precisa permanecer
**disponível**, sobretudo em horários de pico (Denial of Service); e as
**funções administrativas** devem ser acessíveis somente por usuários
autorizados (Elevation of Privilege).

## 6. Casos de abuso

> Visão geral: [Diagrama de casos de abuso](<diagramas/casos-de-abuso.md>)
> (atores maliciosos, casos CA01–CA04 e categorias STRIDE envolvidas).

### CA01 — Sequestro de conta e escalonamento para acesso administrativo

**Ator:** atacante externo (usuário mal-intencionado).

**Objetivo:** assumir a conta de outro usuário e, a partir dela, obter
privilégios de administrador na plataforma.

**Condições necessárias:**

- o login não exige múltiplo fator de autenticação (MFA);
- não há limite de tentativas de login (sem rate limiting);
- existe uma falha de controle de acesso que permite acessar funções
  administrativas sem a devida verificação de permissão.

**Fluxo de abuso:**

1. O atacante obtém uma lista de e-mails e senhas vazados de outro serviço.
2. Automatiza tentativas de login no RapidoFood (credential stuffing), sem ser
   bloqueado por não haver limite de tentativas nem MFA.
3. Consegue acessar a conta de uma vítima e visualiza dados pessoais, endereços
   e histórico de pedidos.
4. Manipula a requisição (por exemplo, alterando o campo de perfil/`role`) ou
   acessa diretamente um endpoint administrativo que não valida a permissão.
5. Passa a ter acesso ao painel administrativo, com visão sobre os dados de
   outros usuários e capacidade de alterar cadastros e pedidos.

**Impacto esperado:** exposição em massa de dados pessoais, realização de
operações fraudulentas, perda de confiança na plataforma e possível controle
administrativo indevido do sistema.

**Categorias STRIDE relacionadas:** Spoofing, Elevation of Privilege e
Information Disclosure.

---

### CA02 — _(título — a preencher por Gustavo)_

> Sugestão de tema (alinhado às categorias do Gustavo): alteração indevida de um
> pedido/valor (Tampering) e/ou negação de uma operação por falta de logs
> confiáveis (Repudiation).

**Ator:** _(preencher)_

**Objetivo:** _(preencher)_

**Condições necessárias:**

- _(preencher)_

**Fluxo de abuso:**

1. _(preencher)_

**Impacto esperado:** _(preencher)_

**Categorias STRIDE relacionadas:** _(ex.: Tampering, Repudiation)_

---

### CA03 — _(título — a preencher por Luiz)_

> Sugestão de tema (alinhado à categoria do Luiz): consulta indevida de dados de
> terceiros por falha de autorização (Information Disclosure), por exemplo
> alterando um identificador de pedido/usuário na requisição.

**Ator:** _(preencher)_

**Objetivo:** _(preencher)_

**Condições necessárias:**

- _(preencher)_

**Fluxo de abuso:**

1. _(preencher)_

**Impacto esperado:** _(preencher)_

**Categorias STRIDE relacionadas:** _(ex.: Information Disclosure)_

---

### CA04 — Ataque de negação de serviço ao aplicativo de delivery

**Ator:** atacante externo utilizando ferramentas automatizadas (bots).

**Objetivo:** tornar o aplicativo indisponível, impedindo que clientes,
restaurantes e entregadores utilizem seus serviços.

**Condições necessárias:**

- o aplicativo está acessível pela internet;
- não há mecanismos suficientes para limitar ou bloquear um grande volume de
  requisições simultâneas.

**Fluxo de abuso:**

1. O atacante utiliza ferramentas automatizadas para enviar milhares de
   requisições simultaneamente ao aplicativo.
2. O servidor recebe um volume excessivo de solicitações e seus recursos ficam
   sobrecarregados.
3. Clientes, restaurantes e entregadores enfrentam lentidão ou ficam
   impossibilitados de acessar e utilizar o sistema.

**Impacto esperado:** interrupção parcial ou total do serviço, perda de pedidos,
prejuízos financeiros para restaurantes e para a plataforma, além da
insatisfação dos usuários.

**Categorias STRIDE relacionadas:** Denial of Service.

## 7. Considerações finais

> **Responsável:** Renata. Estrutura abaixo para preencher com a síntese do grupo.

- **Ameaças mais preocupantes:** _(preencher — ex.: acesso indevido a contas,
  alteração de pedidos/valores, obtenção de privilégios administrativos e
  indisponibilidade em horários de pico.)_
- **Ativos mais importantes:** _(preencher — ex.: credenciais, dados pessoais e
  de pagamento, integridade dos pedidos, localização e disponibilidade.)_
- **Tipos de abuso de maior impacto:** _(preencher)_
- **Principais dificuldades encontradas pelo grupo:** _(preencher — ex.:
  diferenciar uma ameaça genérica de uma situação concreta do sistema.)_
- **(Opcional) Possíveis medidas de proteção:** _(preencher)_
