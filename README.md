# RapidoFood — Aplicativo de Delivery

> Trabalho da disciplina **Engenharia de Software Seguro (EAD)** — **Grupo 1**.
> Análise de segurança de um sistema de software: Casos de Abuso e Modelagem de
> Ameaças com STRIDE.

## 1. Identificação do sistema

- **Nome do sistema:** RapidoFood — aplicativo de delivery de comida.
- **Integrantes (Grupo 1):**
  - Lucas Kaue Ribeiro Weber — _(usuário GitHub a preencher)_
  - Gustavo Borges Arrussul Veiga — _(usuário GitHub a preencher)_
  - Luiz Hermano Bernardes Martins — _(usuário GitHub a preencher)_
  - Renata Vassalo — @renatavassallo
- **Repositório:** https://github.com/corazon261/ES_seguro_EAD
- **Justificativa:** Escolhemos um aplicativo de delivery porque ele reúne, em um
  único sistema, características que tornam a análise de segurança rica e
  realista: possui **quatro perfis de usuário distintos** (cliente, restaurante,
  entregador e administrador), cada um com permissões e interesses diferentes;
  realiza **transações financeiras** por meio de gateways de pagamento; manipula
  **dados pessoais e sensíveis** (endereços, telefone, histórico de consumo) e
  **localização em tempo real**; e depende de trocas de informação entre
  aplicativos móveis, APIs, banco de dados e serviços externos. Essa diversidade
  de atores e de ativos permite identificar ameaças concretas em **todas as seis
  categorias do STRIDE** e elaborar casos de abuso variados.

## 2. Descrição do sistema

O **RapidoFood** é um aplicativo de delivery que conecta pessoas que desejam
pedir comida a restaurantes cadastrados e a entregadores disponíveis. Ele resolve
o problema de intermediar, em um único lugar, todo o processo de um pedido: da
escolha do restaurante até a entrega no endereço do cliente, passando pelo
pagamento e pelo acompanhamento em tempo real.

O sistema é utilizado por quatro perfis principais: o **cliente**, que pesquisa
restaurantes, faz pedidos, paga e avalia; o **restaurante/loja**, que cadastra o
cardápio e prepara os pedidos; o **entregador**, que aceita corridas e realiza a
entrega usando a localização; e o **administrador**, que gerencia a plataforma,
os cadastros e resolve disputas.

Entre as principais funcionalidades estão o cadastro e a autenticação de
usuários, a busca de restaurantes e produtos, a montagem do carrinho e a
realização de pedidos, o pagamento on-line via gateway externo, o acompanhamento
da entrega em mapa (localização em tempo real), o chat entre cliente e
entregador, o sistema de avaliações e um painel administrativo de gestão.

O sistema armazena e transmite dados cadastrais e de contato, endereços de
entrega, credenciais de acesso, dados de pagamento, localização em tempo real,
histórico de pedidos e valores, mensagens do chat e avaliações. Os recursos que
precisam ser protegidos incluem as contas e credenciais, os dados pessoais e de
pagamento, a integridade dos pedidos e valores, a localização dos usuários e a
disponibilidade do serviço.

---

## Organização do repositório

| Arquivo | Conteúdo |
| --- | --- |
| `README.md` | Seções 1 (Identificação) e 2 (Descrição) |
| [Passo 1 - Usuários, ativos e pontos de interação.md](<Passo 1 - Usuários, ativos e pontos de interação.md>) | Seções 3 (usuários, ativos e componentes) e 4 (visão geral da arquitetura e fluxo) |
| [Passo 2 (Entregavel 1) - Modelagem de ameaças e Casos de Abuso.md](<Passo 2 (Entregavel 1) - Modelagem de ameaças e Casos de Abuso.md>) | **Entregável 1** — seções 5 (STRIDE), 6 (Casos de abuso) e 7 (Considerações finais) |
| [docs/divisao-de-tarefas.md](docs/divisao-de-tarefas.md) | Divisão de tarefas do grupo |
| `diagramas/` | Diagramas e seus arquivos-fonte |

### Entregável 1 — situação

- [x] 1. Identificação do sistema
- [x] 2. Descrição do sistema
- [ ] 3. Usuários, ativos e pontos de interação _(Gustavo)_
- [x] 4. Visão geral da arquitetura ou fluxo — diagrama de contexto _(Luiz)_
- [~] 5. Modelagem de ameaças com STRIDE — Spoofing/Elevation (Lucas), DoS (Renata) e Information Disclosure (Luiz) prontos; Tampering/Repudiation (Gustavo) pendentes
- [~] 6. Casos de abuso — CA01 (Lucas), CA03 (Luiz) e CA04 CA06 (Renata) prontos; CA02 (Gustavo) pronto
- [ ] 7. Considerações finais _(Renata)_ Pronto
