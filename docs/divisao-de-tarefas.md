# Divisão de tarefas — Grupo 1

> A avaliação é **individual**: cada integrante deve fazer seus **próprios commits**,
> com mensagens descritivas e associados à sua conta do GitHub. Esta divisão serve
> de guia — colaborem e ajudem uns aos outros, mas cada um é responsável por
> garantir que suas contribuições apareçam no histórico.

Sistema escolhido: **Aplicativo de Delivery** (RapidoFood).

> **Estrutura do repositório** (espelha o exemplo do professor):
> `README.md` (seções 1–2) · `Passo 1 - Usuários, ativos e pontos de interação.md`
> (seção 3) · `Passo 2 (Entregavel 1) - Modelagem de ameaças e Casos de Abuso.md`
> (seções 5, 6 e 7 = **Entregável 1**).

## Distribuição

| Integrante | Seção do documento | Categorias STRIDE | Caso de abuso | Diagrama |
| ---------- | ------------------ | ----------------- | ------------- | -------- |
| **Lucas Kaue Ribeiro Weber** | README (1 Identificação · 2 Descrição) · organização do repositório | Spoofing · Elevation of Privilege | CA01 | Revisão geral |
| **Gustavo Borges Arrussul Veiga** | Passo 1 — seção 3 (Usuários, ativos e pontos de interação) | Tampering · Repudiation | CA02 | Diagrama de fluxo de dados |
| **Luiz Hermano Bernardes Martins** | (apoio ao Passo 1 / visão de fluxo) | Information Disclosure | CA03 | Diagrama de contexto |
| **Renata Vassalo** | Passo 2 — seção 7 (Considerações finais) | Denial of Service | CA04 | Diagrama de casos de abuso |
| **Andre Luiz Montanha de Abreu** | Decisões de Arquitetura & Código Seguro | Tampering / Elevation of Privilege | T11 & CA09 (Fraude de estorno) + Considerações NIST |

### Trabalho colaborativo (arquivo do Entregável 1)

- **Seção 5 — STRIDE (tabela):** cada integrante adiciona as linhas das suas
  categorias. Ao final, a tabela deve cobrir todas as 6 categorias.
- **Seção 6 — Casos de abuso:** cada integrante escreve o seu caso (CA01–CA04).

### Status atual

- ✅ Lucas — README (1 e 2), STRIDE Spoofing/Elevation (T01, T07–T09), CA01.
- ✅ Renata — STRIDE DoS (T05, T06) e CA04.
- ⬜ Gustavo — revisar/expandir seção 3, STRIDE Tampering/Repudiation (T02, T03), CA02.
- ✅ Luiz — STRIDE Information Disclosure (T04, T10), CA03 e seção 4 (visão geral
  da arquitetura + diagrama de contexto).

## Como cada um deve contribuir (passo a passo)

1. Aceitar o convite/ter acesso ao repositório `corazon261/ES_seguro_EAD`.
2. Clonar: `git clone https://github.com/corazon261/ES_seguro_EAD.git`
3. Confirmar sua identidade no git (para o commit sair com seu nome):
   ```bash
   git config user.name "Seu Nome"
   git config user.email "email-associado-ao-github"
   ```
4. Editar as seções sob sua responsabilidade em `docs/modelagem-de-ameacas.md`.
5. Commitar com mensagem descritiva, por exemplo:
   - `Descreve usuários e ativos sensíveis do sistema`
   - `Adiciona ameaças de Tampering e Repudiation`
   - `Inclui caso de abuso CA02 de alteração de pedido`
   - `Adiciona diagrama de fluxo de dados`
6. Enviar: `git pull --rebase` e depois `git push`.

> Preencham o **usuário do GitHub** de cada um no `README.md`.
