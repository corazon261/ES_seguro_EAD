# Etapa 7 — DevSecOps e Vídeo Final

**Sistema:** RapidoFood (app de delivery)

Esta etapa integra os resultados produzidos ao longo do projeto e apresenta
como a segurança pode acompanhar continuamente o ciclo de desenvolvimento.

Não será necessário implementar um pipeline DevSecOps real.

O grupo deverá elaborar:

1. uma descrição textual ou diagrama de um pipeline DevSecOps;
2. o roteiro do vídeo final;
3. o vídeo final apresentando a evolução do projeto.

---

# 1. Pipeline DevSecOps

## 1.1 Planejamento e análise de ameaças

**Responsável: Lucas**

Descrever como a segurança começa no planejamento do projeto, considerando:

- identificação das ameaças;
- casos de abuso;
- ativos importantes;
- riscos identificados na Etapa 1.

**Evidências produzidas:**

- modelagem STRIDE;
- casos de abuso;
- identificação dos ativos e usuários.

**Condição para continuar:**

*(preencher — Lucas)*

---

## 1.2 Requisitos e decisões de arquitetura

**Responsável: Andre**

Descrever como os riscos identificados são transformados em requisitos
de segurança e decisões de arquitetura.

Considerar os resultados da Etapa 3.

**Evidências produzidas:**

- requisitos de segurança;
- vulnerabilidades relacionadas;
- diagrama da arquitetura;
- decisões de arquitetura.

**Condição para continuar:**

*(preencher — Andre)*

---

## 1.3 Implementação segura

**Responsável: Gustavo**

Descrever como as práticas de código seguro da Etapa 4 seriam incorporadas
ao desenvolvimento.

Considerar:

- práticas de código seguro;
- testes definidos antes da implementação;
- referências OWASP;
- correção de problemas encontrados.

**Evidências produzidas:**

- código;
- pseudocódigo;
- testes;
- descrições de implementação.

**Condição para continuar:**

*(preencher — Gustavo)*

---

## 1.4 Testes automatizados e análise de código

**Responsável: Luiz**

Descrever como os testes de segurança e as verificações de código poderiam
ser executados antes da implantação.

Considerar os testes definidos na Etapa 4.

**Evidências produzidas:**

- resultados dos testes;
- registros de aprovação ou reprovação;
- resultados da análise de código, quando aplicável.

**Condição para continuar:**

*(preencher — Luiz)*

---

## 1.5 Teste dinâmico ou verificação de vulnerabilidades

**Responsável: Renata**

Descrever como a verificação realizada na Etapa 5 seria incorporada ao
processo de desenvolvimento.

Considerar:

- ZAP ou ferramenta equivalente;
- evidências da execução;
- análise dos achados;
- correções propostas.

**Evidências produzidas:**

- relatório da verificação;
- capturas de tela;
- análise dos achados;
- propostas de correção.

**Condição para continuar:**

*(preencher — Renata)*

---

## 1.6 Implantação

**Responsável: Andre**

Descrever brevemente quais condições de segurança deveriam ser verificadas
antes da implantação.

Considerar os riscos e controles definidos nas etapas anteriores.

**Condição para continuar:**

*(preencher — Andre)*

---

## 1.7 Monitoramento e resposta

**Responsável: Renata**

Descrever como o sistema seria acompanhado depois da implantação.

Considerar os resultados da Etapa 6:

- registros de eventos;
- regras de detecção;
- alertas;
- resposta inicial aos eventos suspeitos.

**Evidências produzidas:**

- logs;
- alertas;
- registros de incidentes;
- regras de detecção.

**Condição para continuar:**

*(preencher — Renata)*

---

# 2. Condições que impedem a continuidade do pipeline

O grupo deverá indicar pelo menos três condições que impediriam a continuidade
do pipeline.

| Condição | Motivo | Ação necessária |
|---|---|---|
| *(preencher — Gustavo)* | *(preencher — Gustavo)* | *(preencher — Gustavo)* |
| *(preencher — Luiz)* | *(preencher — Luiz)* | *(preencher — Luiz)* |
| *(preencher — Lucas)* | *(preencher — Lucas)* | *(preencher — Lucas)* |

Podem ser consideradas condições como:

- teste de segurança reprovado;
- vulnerabilidade crítica não analisada;
- segredo encontrado no repositório;
- dependência conhecida como vulnerável;
- falha no controle de acesso.

---

# 3. Visão geral do pipeline

O grupo deverá organizar as etapas anteriores em uma sequência lógica.

Exemplo:

```text
Planejamento
     ↓
Análise de ameaças e riscos
     ↓
Requisitos e arquitetura segura
     ↓
Implementação segura
     ↓
Testes e análise de código
     ↓
Verificação de vulnerabilidades
     ↓
Implantação
     ↓
Monitoramento e detecção
     ↓
Resposta e melhoria contínua