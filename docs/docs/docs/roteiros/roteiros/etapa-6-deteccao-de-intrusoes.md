# Etapa 6 — Monitoramento e Detecção de Intrusões

**Sistema:** RapidoFood (app de delivery)

Esta etapa tem como objetivo demonstrar como o sistema poderia identificar
comportamentos suspeitos depois de entrar em operação.

Não será necessário instalar ou implementar um sistema de detecção de
intrusões. O entregável será um roteiro ou descrição textual.

---

## 1. O que é detecção de intrusões

**Responsável: Andre**

Explicar brevemente:

- o que significa detectar uma intrusão;
- qual é o objetivo da detecção;
- por que o monitoramento é importante para o RapidoFood.

*(preencher — Andre)*

---

## 2. Prevenção x detecção

**Responsável: Renata**

Explicar a diferença entre:

- **prevenir:** medidas utilizadas para evitar ou reduzir a possibilidade de
  um incidente;
- **detectar:** identificar comportamentos, eventos ou sinais que indiquem
  uma possível atividade suspeita.

Relacionar a diferença com os riscos identificados nas etapas anteriores.

*(preencher — Renata)*

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
| Risco observado | *(preencher — Luiz)* |
| Fonte de dados | *(preencher — Luiz)* |
| Condição de alerta | *(preencher — Luiz)* |
| Resposta inicial | *(preencher — Luiz)* |

A regra deverá representar um comportamento suspeito relacionado a um dos
riscos do RapidoFood.

---

## 6. Regra de detecção 3

**Responsável: Renata**

| Campo | Conteúdo |
|---|---|
| Risco observado | *(preencher — Renata)* |
| Fonte de dados | *(preencher — Renata)* |
| Condição de alerta | *(preencher — Renata)* |
| Resposta inicial | *(preencher — Renata)* |

A regra deverá representar um comportamento suspeito relacionado a um dos
riscos do RapidoFood.

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
