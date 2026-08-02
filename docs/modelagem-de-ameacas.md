# Modelagem de Ameaças e Casos de Abuso — Aplicativo de Delivery

> **Como usar este arquivo:** cada seção abaixo corresponde a um item obrigatório
> do enunciado (4.1 a 4.7). Os textos em _itálico entre parênteses_ e os
> comentários `<!-- ... -->` são instruções e devem ser substituídos/removidos.
> As linhas marcadas com **(exemplo)** nas tabelas são apenas modelos — troquem
> pelo conteúdo real do grupo.

---

## 4.1 Identificação do sistema

- **Nome do sistema:** _(ex.: "RapidoFood" — app de delivery)_
- **Integrantes do grupo (Grupo 1):**
  - Lucas Kaue Ribeiro Weber
  - Gustavo Borges Arrussul Veiga
  - Luiz Hermano Bernardes Martins
  - Renata Vassalo
- **Endereço do repositório:** https://github.com/corazon261/ES_seguro_EAD
- **Justificativa para a escolha do sistema:**
  _(Por que escolheram um app de delivery? Sugestão: possui múltiplos perfis de
  usuário — cliente, restaurante, entregador e administrador —, envolve
  pagamentos, dados pessoais, localização em tempo real e avaliações, o que
  permite identificar ameaças em todas as categorias do STRIDE.)_

---

## 4.2 Descrição do sistema

_(Descreva o funcionamento do app de delivery de forma que alguém que não o
conhece entenda. Responder, no texto corrido, aos pontos abaixo:)_

- **Qual problema o sistema resolve?** _(conectar clientes a restaurantes e
  entregadores para pedir e receber comida/produtos.)_
- **Quem utiliza o sistema?** _(clientes, restaurantes/lojas, entregadores,
  administradores da plataforma.)_
- **Principais funcionalidades:** _(cadastro/login, buscar restaurantes, montar
  carrinho, pagar, acompanhar entrega no mapa, avaliar, gerenciar cardápio,
  aceitar corridas de entrega, etc.)_
- **Informações armazenadas ou transmitidas:** _(dados cadastrais, endereço,
  localização, dados de pagamento, histórico de pedidos, avaliações, mensagens.)_
- **Recursos que precisam ser protegidos:** _(ver seção 4.3.)_

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
| T01 | Spoofing | Conta do usuário | _(exemplo)_ Atacante usa credenciais roubadas para acessar a conta de outra pessoa | Acesso a dados privados e pedidos fraudulentos |
| T02 | Tampering | Pedido | _(exemplo)_ Usuário altera o valor do pedido antes do pagamento | Prejuízo financeiro |
| T03 | Repudiation | _(preencher)_ | _(preencher)_ | _(preencher)_ |
| T04 | Information Disclosure | Banco de dados | _(exemplo)_ Dados pessoais expostos por falha de autorização | Violação de privacidade |
| T05 | Denial of Service | API de pedidos | Um atacante envia um grande volume de requisições simultâneas para a API responsável pelo processamento de pedidos, consumindo os recursos do servidor e impedindo que novas solicitações legítimas sejam atendidas. | Clientes não conseguem realizar pedidos, restaurantes deixam de receber novas solicitações e ocorre indisponibilidade parcial do serviço. |
| T06 | Denial of Service | Servidor de autenticação | Um atacante realiza milhares de tentativas simultâneas de login, sobrecarregando o servidor de autenticação e impedindo que usuários legítimos acessem suas contas. | Clientes, restaurantes e entregadores ficam impossibilitados de acessar o aplicativo, interrompendo o funcionamento da plataforma. |
| T07 |

> **Dica:** tente ter pelo menos 1–2 ameaças por categoria (6 a 12 no total).

---

## 4.6 Casos de abuso

_(Cada caso de abuso deve conter: identificador, título, ator, objetivo,
condições, sequência de ações, impacto e categorias STRIDE relacionadas.
Modelo abaixo — dupliquem para cada caso.)_

### CA01 — _(título do caso de abuso)_

- **Ator malicioso / agente:** _(preencher)_
- **Objetivo do abuso:** _(preencher)_
- **Condições necessárias:** _(preencher)_
- **Sequência de ações (fluxo de abuso):**
  1. _(preencher)_
  2. _(preencher)_
  3. _(preencher)_
- **Impacto esperado:** _(preencher)_
- **Categorias STRIDE relacionadas:** _(preencher — ex.: Spoofing, Information Disclosure)_

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
