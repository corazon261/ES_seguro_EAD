# Plano de trabalho — Grupo 1 (disciplina completa)

> **Prazo final:** 14 de agosto de 2026, 23:59.
> **Sistema:** RapidoFood (app de delivery) — o **mesmo** sistema e o **mesmo**
> repositório são usados em **todas** as etapas.
> **Regra de ouro:** a avaliação é **individual por commits**. Cada integrante
> precisa ter commits próprios, com mensagens descritivas, associados à sua conta
> do GitHub. Não apagar o conteúdo de etapas anteriores — cada etapa é **somada**
> ao que já existe.

## Integrantes (Grupo 1)

| Integrante | GitHub | Já tem commits? |
| --- | --- | --- |
| Lucas Kaue Ribeiro Weber | @corazon261 | ✅ |
| Gustavo Borges Arrussul Veiga | @GustavoArrussul | ✅ |
| Luiz Hermano Bernardes Martins | @LuizHerm | ✅ |
| Renata Rodrigues Vassalo da Rosa | @renatavassallo | ✅ |
| **Andre Luiz Montanha de Abreu** | _(preencher)_ | ⬜ **ainda não — prioridade** |

> **Andre:** primeiro passo é clonar o repo e configurar o git com o e-mail da
> sua conta do GitHub (veja "Como contribuir" no fim deste arquivo), para que
> seus commits contem como participação individual.

## Visão geral das 7 etapas

| Etapa | Entregável mínimo | Arquivo sugerido | Status |
| --- | --- | --- | --- |
| **1 — Ameaças STRIDE e Casos de Abuso** | Descrição, ativos, STRIDE, casos de abuso, diagramas | `Passo 1...md`, `Passo 2 (Entregavel 1)...md` | ✅ **Concluída** |
| **2 — Análise, Priorização e Tratamento de Riscos (NIST CSF)** | Critérios de prob./impacto, registro de riscos, priorização, tratamento, mapeamento NIST, risco residual | `docs/etapa-2-riscos-nist.md` | ⬜ **Próximo foco** |
| **3 — Projeto de Arquitetura Segura** | 3 requisitos de segurança, 3 vulnerabilidades (CWE/OWASP), 1 diagrama, 3 decisões | `docs/etapa-3-arquitetura-segura.md` + `diagramas/etapa-3/` | ⬜ |
| **4 — Código Seguro e Testes** | 2 práticas de código seguro, cada uma com 2 testes definidos antes | `docs/etapa-4-codigo-seguro.md` + `codigo/etapa-4/` | ⬜ |
| **5 — Verificação de Vulnerabilidades** | 1 sessão com ferramenta (ZAP), análise de 3 achados + correções | `evidencias/etapa-5/relatorio-da-verificacao.md` | ⬜ |
| **6 — Monitoramento e Detecção de Intrusões** | Roteiro textual + 3 regras de detecção | `roteiros/etapa-6-deteccao-de-intrusoes.md` | ⬜ |
| **7 — DevSecOps e Vídeo Final** | Pipeline (texto/diagrama) + roteiro + vídeo de 5–8 min | `roteiros/etapa-7-devsecops-e-video-final.md` | ⬜ |

> As Etapas 3 a 7 foram **reduzidas ao mínimo** pelo professor. Não é preciso
> implementar sistema completo: pseudocódigo, descrições e diagramas são aceitos.

## Divisão de tarefas por integrante

A ideia é aproveitar o que cada um já "domina" da Etapa 1 e distribuir as demais.

| Integrante | Etapa 2 (riscos) | Outras etapas (responsável principal) |
| --- | --- | --- |
| **Lucas** | Riscos de Spoofing/Elevation (T01, T07–T09) | Etapa 3 (diagrama de arquitetura + vulnerabilidades CWE/OWASP); Etapa 5 (análise dos achados) |
| **Gustavo** | Riscos de Tampering/Repudiation (T02, T03) | Etapa 4 (prática 1) ; Etapa 7 (pipeline DevSecOps) |
| **Luiz** | Riscos de Information Disclosure (T04, T10) | Etapa 4 (prática 2) ; Etapa 6 (roteiro de detecção) |
| **Renata** | Riscos de Denial of Service (T05, T06) | Etapa 5 (execução do ZAP + evidências) |
| **Andre** | **Arcabouço da Etapa 2**: critérios de probabilidade e impacto, tabela de cálculo, introdução às funções do NIST CSF e considerações finais | Etapa 3 (3 requisitos + 3 decisões de arquitetura) |
| **Todos** | Cada um justifica seus próprios riscos e faz o mapeamento NIST das suas linhas | Etapa 7 (vídeo final) |

> Divisão sugerida — ajustem entre vocês. O importante é que cada nome apareça no
> histórico contribuindo com conteúdo relevante.

## Detalhe da Etapa 2 (próximo entregável)

Crie um arquivo novo (ex.: `docs/etapa-2-riscos-nist.md`) — **não** altere os
arquivos da Etapa 1. Checklist dos 13 itens mínimos:

- [ ] 1. Critérios de **probabilidade** (escala 1–4) — _Andre_
- [ ] 2. Critérios de **impacto** (escala 1–4) — _Andre_
- [ ] 3. **Cálculo e classificação** (Pontuação = Prob. × Impacto; Baixo/Médio/Alto/Crítico) — _Andre_
- [ ] 4. **Registro de riscos** (R01, R02… a partir das ameaças da Etapa 1) — _todos, cada um os seus_
- [ ] 5. **Justificativas** de cada probabilidade/impacto — _todos, cada um os seus_
- [ ] 6. **Priorização** dos riscos — _Lucas coordena, com base nas notas_
- [ ] 7. **Estratégias de tratamento** (evitar/reduzir/compartilhar/aceitar) — _todos, cada um os seus_
- [ ] 8. Apresentação das **6 funções do NIST CSF 2.0** (Govern, Identify, Protect, Detect, Respond, Recover) — _Andre_
- [ ] 9. **Mapeamento** risco → funções do NIST — _todos, cada um os seus_
- [ ] 10. **Plano de tratamento** (controles concretos, responsáveis, verificação) — _todos, cada um os seus_
- [ ] 11. **Ordem inicial** de implementação (justificada) — _Gustavo_
- [ ] 12. **Estimativa de risco residual** — _Renata_
- [ ] 13. **Considerações finais** — _Andre_

> **Dica de coerência:** cada risco (R0x) deve apontar para a ameaça STRIDE
> (T0x) ou caso de abuso (CA0x) de origem. Escala: Pontuação = Probabilidade
> (1–4) × Impacto (1–4) → 1–3 Baixo, 4–7 Médio, 8–11 Alto, 12–16 Crítico.

## Estrutura de pastas recomendada (a criar nas próximas etapas)

```
ES_seguro_EAD/
├── README.md
├── Passo 1 ... .md                 # Etapa 1 (feito)
├── Passo 2 (Entregavel 1) ... .md  # Etapa 1 (feito)
├── docs/
│   ├── plano-de-trabalho.md        # este arquivo
│   ├── divisao-de-tarefas.md       # divisão da Etapa 1
│   ├── etapa-2-riscos-nist.md      # Etapa 2
│   ├── etapa-3-arquitetura-segura.md
│   └── etapa-4-codigo-seguro.md
├── diagramas/
│   ├── (diagramas da Etapa 1)
│   └── etapa-3/                     # arquitetura segura (fonte + imagem)
├── codigo/
│   └── etapa-4/                     # implementação/pseudocódigo + testes
├── evidencias/
│   └── etapa-5/                     # capturas de tela + relatorio-da-verificacao.md
└── roteiros/
    ├── etapa-6-deteccao-de-intrusoes.md
    └── etapa-7-devsecops-e-video-final.md
```

## Lembretes importantes

- **Etapa 5** só pode testar o **próprio sistema**, um ambiente **autorizado** ou
  uma app propositalmente vulnerável (ex.: **OWASP Juice Shop** com o **ZAP**).
  É proibido testar sistemas de terceiros sem autorização.
- **Controles** (Etapa 2 em diante) devem ser **específicos e verificáveis** —
  evitem "aumentar a segurança", "usar criptografia", "aplicar o NIST".
- Sempre fazer `git pull --rebase` antes de `git push` para evitar conflitos.

## Como contribuir (git) — especialmente para o Andre

```bash
git clone https://github.com/corazon261/ES_seguro_EAD.git
cd ES_seguro_EAD
git config user.name "Andre Luiz Montanha de Abreu"
git config user.email "email-da-sua-conta-do-github"
# editar os arquivos da sua parte
git add .
git commit -m "Descreve o que você fez (ex.: Define criterios de probabilidade e impacto - Etapa 2)"
git pull --rebase
git push
```
