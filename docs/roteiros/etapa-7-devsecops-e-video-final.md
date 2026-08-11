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

Nesta etapa do pipeline, todo *commit* enviado ao repositório dispararia
automaticamente três verificações, executadas em sequência e sem intervenção
manual:

1. **Testes de segurança automatizados** — a suíte definida na Etapa 4 roda
   contra uma instância de teste da API. Os casos TS03 e TS04 são os mais
   relevantes: o TS03 confirma que o dono do pedido continua conseguindo
   consultá-lo, e o TS04 confirma que a consulta ao pedido de outro usuário
   recebe `403 Forbidden`. O TS04 funciona como teste de regressão do R06 — se
   alguém reintroduzir a falha de autorização, ele falha e barra a entrega.
2. **Análise estática de código (SAST)** — varredura do código em busca de
   padrões conhecidos de vulnerabilidade, com atenção às consultas que recebem
   identificadores vindos da requisição sem verificação de propriedade.
3. **Verificação de dependências** — checagem das bibliotecas usadas contra
   bases públicas de vulnerabilidades conhecidas.

**Evidências produzidas:**

- resultados dos testes;
- registros de aprovação ou reprovação;
- resultados da análise de código, quando aplicável.

**Condição para continuar:**

O pipeline só avança para a etapa seguinte se **todos os testes de segurança
passarem** e se a análise estática **não apontar nenhum achado de severidade
alta ou crítica**. Qualquer falha interrompe a esteira, e a versão não segue
para implantação.

A reprovação não pode ser ignorada nem contornada manualmente: se a equipe
decidir seguir mesmo assim, essa exceção precisa ser registrada com
justificativa e aprovação de um responsável, conforme a função *Govern* do NIST
CSF. Um portão de qualidade que qualquer pessoa pode pular não protege nada.

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
| Teste de autorização reprovado (TS04): a API devolveu dados de um pedido para quem não é o dono, em vez de `403 Forbidden` | A falha de autorização a nível de objeto é a origem do R06, o risco crítico do registro. Publicar nesse estado exporia dados pessoais de todos os clientes e configuraria tratamento irregular perante a LGPD | Interromper o pipeline e bloquear a implantação. Corrigir a verificação de propriedade do objeto no servidor, rodar a suíte novamente e só liberar após o TS04 passar. A exceção não pode ser concedida por quem escreveu o código |
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