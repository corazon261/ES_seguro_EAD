# Modelagem de Ameaças e Casos de Abuso — Aplicativo de Delivery

> **Como usar este arquivo:** cada seção abaixo corresponde a um item obrigatório
> do enunciado (4.1 a 4.7). Os textos em _itálico entre parênteses_ e os
> comentários `<!-- ... -->` são instruções e devem ser substituídos/removidos.
> As linhas marcadas com **(exemplo)** nas tabelas são apenas modelos — troquem
> pelo conteúdo real do grupo.

---

## 4.1 Identificação do sistema

- **Nome do sistema:** RapidoFood — aplicativo de delivery de comida.
- **Integrantes do grupo (Grupo 1):**
  - Lucas Kaue Ribeiro Weber
  - Gustavo Borges Arrussul Veiga
  - Luiz Hermano Bernardes Martins
  - Renata Vassalo
- **Endereço do repositório:** https://github.com/corazon261/ES_seguro_EAD
- **Justificativa para a escolha do sistema:**
  Escolhemos um aplicativo de delivery porque ele reúne, em um único sistema,
  características que tornam a análise de segurança rica e realista: possui
  **quatro perfis de usuário distintos** (cliente, restaurante, entregador e
  administrador), cada um com permissões e interesses diferentes; realiza
  **transações financeiras** por meio de gateways de pagamento; manipula
  **dados pessoais e sensíveis** (endereços, telefone, histórico de consumo) e
  **localização em tempo real** de clientes e entregadores; e depende de trocas
  de informação entre aplicativos móveis, APIs, banco de dados e serviços
  externos. Essa diversidade de atores e de ativos permite identificar ameaças
  concretas em **todas as seis categorias do STRIDE** e elaborar casos de abuso
  variados, o que atende aos objetivos da atividade.

---

## 4.2 Descrição do sistema

O **RapidoFood** é um aplicativo de delivery que conecta pessoas que desejam
pedir comida a restaurantes cadastrados e a entregadores disponíveis. Ele
resolve o problema de intermediar, em um único lugar, todo o processo de um
pedido: da escolha do restaurante até a entrega no endereço do cliente, passando
pelo pagamento e pelo acompanhamento em tempo real.

- **Qual problema o sistema resolve?** Facilita e organiza o pedido e a entrega
  de comida, aproximando clientes, restaurantes e entregadores e cuidando do
  pagamento, da logística de entrega e da comunicação entre as partes.

- **Quem utiliza o sistema?** Quatro perfis principais:
  - **Cliente** — pesquisa restaurantes, faz pedidos, paga e avalia;
  - **Restaurante / Loja** — cadastra o cardápio, recebe e prepara os pedidos;
  - **Entregador** — aceita corridas e realiza a entrega usando a localização;
  - **Administrador** — gerencia a plataforma, os cadastros e resolve disputas.

- **Principais funcionalidades:** cadastro e autenticação de usuários; busca de
  restaurantes e produtos; montagem do carrinho e realização de pedidos;
  pagamento on-line via gateway externo; acompanhamento da entrega em mapa
  (localização em tempo real); chat entre cliente e entregador; sistema de
  avaliações e notas; e um painel administrativo de gestão.

- **Informações armazenadas ou transmitidas:** dados cadastrais e de contato
  (nome, e-mail, telefone), endereços de entrega, credenciais de acesso, dados
  de pagamento, localização em tempo real, histórico de pedidos e valores,
  mensagens do chat e avaliações.

- **Recursos que precisam ser protegidos:** contas e credenciais, dados pessoais
  e de pagamento, a integridade dos pedidos e valores, a localização dos
  usuários e a disponibilidade do serviço (detalhados na seção 4.3).

---

## 4.3 Usuários, ativos e pontos de interação

### Perfis de usuário

| Perfil | Descrição | Principais permissões |
| ------ | --------- | --------------------- |
| Cliente | _(preencher)_ | _(preencher)_ |
| Restaurante / Loja | _(preencher)_ | _(preencher)_ |
| Entregador | _(preencher)_ | _(preencher)_ |
| Administrador | _(preencher)_ | _(preencher)_ |

### Ativos e pontos de interação

_(Liste os elementos do sistema e marque quais são **ativos importantes** —
recursos que causam prejuízo se acessados, alterados, destruídos ou
indisponibilizados indevidamente.)_

| Elemento | É ativo importante? | Por quê |
| -------- | ------------------- | ------- |
| Credenciais / contas | Sim _(exemplo)_ | _(acesso indevido a contas)_ |
| Dados pessoais (nome, telefone, endereço) | _(preencher)_ | _(preencher)_ |
| Dados de pagamento | _(preencher)_ | _(preencher)_ |
| Localização em tempo real | _(preencher)_ | _(preencher)_ |
| Pedidos e valores | _(preencher)_ | _(preencher)_ |
| Avaliações / notas | _(preencher)_ | _(preencher)_ |
| Mensagens (chat cliente↔entregador) | _(preencher)_ | _(preencher)_ |
| Banco de dados | _(preencher)_ | _(preencher)_ |
| APIs / servidores | _(preencher)_ | _(preencher)_ |
| Serviços externos (gateway de pagamento, mapas) | _(preencher)_ | _(preencher)_ |

---

## 4.4 Visão geral da arquitetura ou fluxo

_(Apresente uma visão simplificada de como usuários e componentes interagem.
Pode ser texto, tabela ou diagrama. Recomenda-se criar um diagrama e salvá-lo
em `diagramas/`. Ver `diagramas/README.md` para sugestões.)_

<!-- Quando o diagrama estiver pronto, referencie a imagem assim:
![Diagrama de contexto](../diagramas/diagrama-contexto.png)
-->

**Fluxo resumido (texto — substituir/expandir):**

1. _(O cliente faz login e busca restaurantes.)_
2. _(Monta o carrinho e realiza o pagamento via gateway externo.)_
3. _(O restaurante recebe e aceita o pedido.)_
4. _(Um entregador aceita a corrida e recebe a localização de entrega.)_
5. _(O cliente acompanha a entrega no mapa e avalia ao final.)_

---

## 4.5 Modelagem de ameaças com STRIDE

_(Identifique ameaças **concretas** para cada categoria. Todas as 6 categorias
devem ser analisadas; se alguma não se aplicar, justifique. As linhas abaixo são
exemplos — substituam por ameaças do grupo.)_

| ID | Categoria STRIDE | Componente ou ativo | Ameaça identificada | Possível impacto |
| --- | ---------------- | ------------------- | ------------------- | ---------------- |
| T01 | Spoofing | Conta do usuário | Um atacante utiliza credenciais roubadas (vazamento de outro serviço, phishing ou credential stuffing) para acessar a conta de outra pessoa, já que o login não exige múltiplo fator de autenticação. | Acesso a dados pessoais e endereços, realização de pedidos fraudulentos e uso do saldo/cartão salvo da vítima. |
| T02 | Tampering | Pedido | _(exemplo)_ Usuário altera o valor do pedido antes do pagamento | Prejuízo financeiro |
| T03 | Repudiation | _(preencher)_ | _(preencher)_ | _(preencher)_ |
| T04 | Information Disclosure | Banco de dados | _(exemplo)_ Dados pessoais expostos por falha de autorização | Violação de privacidade |
| T05 | Denial of Service | API de pedidos | Um atacante envia um grande volume de requisições simultâneas para a API responsável pelo processamento de pedidos, consumindo os recursos do servidor e impedindo que novas solicitações legítimas sejam atendidas. | Clientes não conseguem realizar pedidos, restaurantes deixam de receber novas solicitações e ocorre indisponibilidade parcial do serviço. |
| T06 | Denial of Service | Servidor de autenticação | Um atacante realiza milhares de tentativas simultâneas de login, sobrecarregando o servidor de autenticação e impedindo que usuários legítimos acessem suas contas. | Clientes, restaurantes e entregadores ficam impossibilitados de acessar o aplicativo, interrompendo o funcionamento da plataforma. |
| T07 | Spoofing | Localização do entregador | Um entregador mal-intencionado falsifica o sinal de GPS (GPS spoofing) para simular que está em um local ou que concluiu uma entrega sem tê-la realizado. | Confirmação de entregas falsas, prejuízo a clientes e restaurantes e fraude no repasse de valores. |
| T08 | Elevation of Privilege | Controle de acesso da API | Um usuário comum manipula o identificador de papel/perfil na requisição (ex.: campo `role`) ou acessa um endpoint administrativo sem verificação de autorização (IDOR/broken access control), obtendo permissões de administrador. | Acesso ao painel administrativo, aos dados de todos os usuários e à possibilidade de alterar cadastros e pedidos. |
| T09 | Elevation of Privilege | Perfil de entregador/restaurante | Um entregador ou restaurante explora falhas de autorização para executar ações restritas a outro perfil (ex.: cancelar pedidos de terceiros ou editar cardápios de outra loja). | Manipulação indevida de pedidos e cardápios, prejuízo financeiro e quebra da separação de responsabilidades entre perfis. |

> **Dica:** tente ter pelo menos 1–2 ameaças por categoria (6 a 12 no total).

---

## 4.6 Casos de abuso

_(Cada caso de abuso deve conter: identificador, título, ator, objetivo,
condições, sequência de ações, impacto e categorias STRIDE relacionadas.
Modelo abaixo — dupliquem para cada caso.)_

### CA01 — Sequestro de conta e escalonamento para acesso administrativo

- **Ator malicioso / agente:** Atacante externo (usuário mal-intencionado).
- **Objetivo do abuso:** Assumir a conta de outro usuário e, a partir dela,
  obter privilégios de administrador na plataforma.
- **Condições necessárias:** O login não exige múltiplo fator de autenticação
  (MFA); não há limite de tentativas de login (sem rate limiting); e existe uma
  falha de controle de acesso que permite acessar funções administrativas sem a
  devida verificação de permissão.
- **Sequência de ações (fluxo de abuso):**
  1. O atacante obtém uma lista de e-mails e senhas vazados de outro serviço.
  2. Automatiza tentativas de login no RapidoFood (credential stuffing), sem ser
     bloqueado por não haver limite de tentativas nem MFA.
  3. Consegue acessar a conta de uma vítima e visualiza dados pessoais,
     endereços e histórico de pedidos.
  4. Manipula a requisição (por exemplo, alterando o campo de perfil/`role`) ou
     acessa diretamente um endpoint administrativo que não valida a permissão do
     usuário.
  5. Passa a ter acesso ao painel administrativo, com visão sobre os dados de
     outros usuários e capacidade de alterar cadastros e pedidos.
- **Impacto esperado:** Exposição em massa de dados pessoais, realização de
  operações fraudulentas, perda de confiança na plataforma e possível controle
  administrativo indevido do sistema.
- **Categorias STRIDE relacionadas:** Spoofing, Elevation of Privilege e
  Information Disclosure.

### CA02 — _(título)_

<!-- Copie o bloco do CA01 e preencha -->

### CA03 — _(título)_

<!-- Copie o bloco do CA01 e preencha -->

### CA04 — Ataque de negação de serviço ao aplicativo de delivery

- **Ator malicioso / agente:** Atacante externo utilizando ferramentas automatizadas (bots).
- **Objetivo do abuso:** Tornar o aplicativo indisponível, impedindo que clientes, restaurantes e entregadores utilizem seus serviços.
- **Condições necessárias:** O aplicativo deve estar acessível pela internet e não possuir mecanismos suficientes para limitar ou bloquear um grande volume de requisições simultâneas.
  
- **Sequência de ações (fluxo de abuso):**
  
1. O atacante utiliza ferramentas automatizadas para enviar milhares de requisições simultaneamente ao aplicativo.
2. O servidor recebe um volume excessivo de solicitações e seus recursos ficam sobrecarregados.
3. Clientes, restaurantes e entregadores enfrentam lentidão ou ficam impossibilitados de acessar e utilizar o sistema.
- **Impacto esperado:**
   Interrupção parcial ou total do serviço, perda de pedidos, prejuízos financeiros para restaurantes e para a plataforma, além da insatisfação dos usuários.
  
- **Categorias STRIDE relacionadas:**
   Denial of Service (DoS).

---

## 4.7 Considerações finais

_(Faça uma síntese abordando:)_

- **Ameaças mais preocupantes:** _(preencher)_
- **Ativos mais importantes:** _(preencher)_
- **Tipos de abuso de maior impacto:** _(preencher)_
- **Principais dificuldades encontradas pelo grupo:** _(preencher)_
- **(Opcional) Possíveis medidas de proteção:** _(preencher)_
