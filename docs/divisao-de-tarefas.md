# Divisão de tarefas — Grupo 1

> A avaliação é **individual**: cada integrante deve fazer seus **próprios commits**,
> com mensagens descritivas e associados à sua conta do GitHub. Esta divisão serve
> de guia — colaborem e ajudem uns aos outros, mas cada um é responsável por
> garantir que suas contribuições apareçam no histórico.

Sistema escolhido: **Aplicativo de Delivery**.

## Distribuição

| Integrante | Seções do documento | Categorias STRIDE | Caso de abuso | Diagrama |
| ---------- | ------------------- | ----------------- | ------------- | -------- |
| **Lucas Kaue Ribeiro Weber** | 4.1 Identificação · 4.2 Descrição · organização do repositório | Spoofing · Elevation of Privilege | CA01 | Revisão geral |
| **Gustavo Borges Arrussul Veiga** | 4.3 Usuários, ativos e pontos de interação | Tampering · Repudiation | CA02 | Diagrama de fluxo de dados |
| **Luiz Hermano Bernardes Martins** | 4.4 Visão geral da arquitetura / fluxo | Information Disclosure | CA03 | Diagrama de contexto |
| **Renata Vassalo** | 4.7 Considerações finais | Denial of Service | CA04 | Diagrama de casos de abuso |

### Seção colaborativa

- **4.5 Modelagem de ameaças com STRIDE (tabela):** cada integrante adiciona as
  linhas correspondentes às suas categorias STRIDE. Ao final, a tabela deve
  cobrir todas as 6 categorias.
- **4.6 Casos de abuso:** cada integrante escreve o seu caso (CA01–CA04) seguindo
  o modelo do documento.

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
