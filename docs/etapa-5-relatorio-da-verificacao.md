# Etapa 5 — Verificação de Vulnerabilidades
 
**Sistema/ambiente testado:** OWASP Juice Shop 20.2.0, executado localmente para fins educacionais.
 
---
 
## 1. Ambiente testado
 
**Responsável:** Renata
 
| Campo | Informação |
|---|---|
| Sistema ou aplicação testada | OWASP Juice Shop 20.2.0 |
| Ambiente utilizado | Aplicação executada localmente em Windows 64 bits |
| Autorização para o teste | Ambiente local e aplicação deliberadamente vulnerável utilizada para fins educacionais |
| Objetivo da verificação | Utilizar o OWASP ZAP para identificar vulnerabilidades, configurações inseguras e alertas de segurança na aplicação |
 
A aplicação foi executada localmente, sem realização de testes contra sistemas de terceiros.
 
---
 
## 2. Ferramenta utilizada
 
**Responsável:** Renata
 
| Campo | Informação |
|---|---|
| Ferramenta | OWASP ZAP |
| Versão | 2.17.0 |
| Tipo de verificação realizada | Verificação de segurança da aplicação web, incluindo análise passiva dos recursos acessados |
| Configuração básica utilizada | OWASP Juice Shop executado localmente em `http://localhost:3000`, com análise realizada pelo ZAP |
 
Durante a preparação do ambiente, a aplicação foi iniciada utilizando:
 
```
npm.cmd start
```
 
O servidor iniciou corretamente e apresentou a mensagem:
 
```
Server listening on port 3000
```
 
A inicialização também confirmou que as dependências e os arquivos necessários da aplicação estavam presentes.
 
---
 
## 3. Execução da verificação
 
**Responsável:** Renata
 
O ambiente de testes foi iniciado localmente utilizando a versão 20.2.0 do OWASP Juice Shop. A aplicação foi executada em ambiente Windows 64 bits utilizando Node.js v24.19.0.
 
A aplicação ficou disponível em:
 
```
http://localhost:3000
```
 
Após a inicialização do Juice Shop, foi utilizado o OWASP ZAP para realizar a verificação de segurança da aplicação.
 
Durante a execução, o ZAP identificou diferentes alertas relacionados à configuração e segurança da aplicação.
 
Ao final da verificação foram observados:
 
- **4** alertas de prioridade média
- **5** alertas de baixa prioridade
- **2** alertas informacionais
Não foram identificados alertas classificados como críticos durante a execução realizada.
 
### Evidências
 
As capturas de tela realizadas durante a execução deverão ser armazenadas em:
 
```
evidencias/etapa-5/
```
 
Foram realizadas capturas das telas relevantes do ZAP e das evidências dos achados selecionados para análise.
 
---
 
## 4. Achados da verificação
 
Os três achados abaixo foram selecionados entre os resultados observados no ZAP para análise nesta etapa.
 
### A01 — Configuração incorreta de CORS
 
**Responsável pela análise:** Renata
 
| Campo | Informação |
|---|---|
| Alerta ou achado | Configuração Incorreta Entre Domínios — CORS |
| Identificação do ZAP | 10098 |
| Prioridade | Média |
| Confiança | Alta |
| CWE | CWE-264 |
| WASC | WASC-14 |
| Fonte | Passivo (10098 - Configuração Incorreta Entre Domínios) |
| Evidência | Configuração CORS permitindo solicitações de origem cruzada de domínios arbitrários |
| Possível impacto | Pode permitir que recursos da aplicação sejam acessados por origens não autorizadas, aumentando o risco de exposição de informações disponíveis por APIs não autenticadas |
| Correção proposta | Restringir as origens permitidas pelo CORS, evitando configurações que aceitem domínios arbitrários e permitindo somente origens necessárias |
 
**Análise**
 
O ZAP identificou uma possível configuração inadequada de CORS. O alerta indica que o servidor pode permitir solicitações de leitura entre domínios de terceiros arbitrários.
 
O próprio alerta ressalta que as restrições dos navegadores reduzem o risco quando as APIs exigem autenticação, mas a configuração ainda pode possibilitar acesso indevido a informações disponibilizadas sem autenticação.
 
---
 
### A02 — Content Security Policy Header Not Set
 
**Responsável pela análise:** Renata
 
| Campo | Informação |
|---|---|
| Alerta ou achado | Content Security Policy (CSP) Header Not Set |
| Identificação do ZAP | 10038 |
| Prioridade | Média |
| Confiança | Alta |
| CWE | CWE-693 |
| WASC | WASC-15 |
| Fonte | Passivo (10038 - Content Security Policy (CSP) Header Not Set) |
| Evidência | Ausência do cabeçalho `Content-Security-Policy` |
| Possível impacto | Redução da proteção do navegador contra determinados ataques, incluindo XSS e ataques de injeção de conteúdo |
| Correção proposta | Configurar o servidor ou componente responsável para enviar um cabeçalho `Content-Security-Policy` adequado à aplicação |
 
**Análise**
 
O ZAP identificou que a aplicação não estava fornecendo o cabeçalho de segurança `Content-Security-Policy`.
 
A CSP funciona como uma camada adicional de proteção, permitindo definir quais fontes de conteúdo podem ser carregadas pelo navegador.
 
A ausência desse cabeçalho não significa, por si só, que exista uma vulnerabilidade XSS explorável, mas representa uma configuração de segurança ausente que reduz uma camada de defesa da aplicação.
 
---
 
### A03 — Session ID in URL Rewrite
 
**Responsável pela análise:** Renata
 
| Campo | Informação |
|---|---|
| Alerta ou achado | Session ID in URL Rewrite |
| Identificação do ZAP | 3 |
| Referência do alerta | 3-1 |
| Prioridade | Média |
| Confiança | Alta |
| Parâmetro | `sid` |
| CWE | CWE-598 |
| WASC | WASC-13 |
| Fonte | Passivo (3 - Session ID in URL Rewrite) |
| Evidência | `http://localhost:3000/socket.io/?EIO=4&transport=polling&t=...&sid=...` |
| Possível impacto | O identificador de sessão presente na URL pode ser armazenado no histórico do navegador, logs ou eventualmente exposto por mecanismos como o cabeçalho Referer |
| Correção proposta | Evitar colocar identificadores de sessão diretamente na URL quando possível e utilizar mecanismos apropriados para gerenciamento de sessão |
 
**Análise**
 
Durante a verificação, o ZAP identificou o parâmetro `sid` presente na URL utilizada pelo Socket.IO.
 
O alerta indica que identificadores de sessão presentes em URLs podem ser expostos por mecanismos como histórico do navegador, logs do servidor ou informações de referência.
 
---
 
## 5. Relação com os riscos anteriores
 
**Responsável:** Renata
 
| Achado | Risco relacionado | Ameaça STRIDE / caso de abuso | Relação |
|---|---|---|---|
| A01 — CORS | R06 — Exposição de dados de terceiros | Information Disclosure (T04, CA03) | A configuração inadequada de CORS pode ampliar a possibilidade de acesso a recursos da aplicação por origens não autorizadas, dependendo de como os endpoints são protegidos. |
| A02 — CSP | R04 — Alteração de valores / Tampering | Tampering (T02, CA02) | A ausência de CSP não corresponde diretamente ao cenário de alteração de preços, mas representa uma camada de proteção ausente contra determinados ataques de injeção de conteúdo. A relação é indireta. |
| A03 — Session ID in URL Rewrite | R06 — Exposição de dados de terceiros | Information Disclosure (T04, CA03) | A exposição do identificador de sessão em URL pode contribuir para divulgação indevida de informações de sessão e, dependendo do contexto, aumentar o risco de acesso indevido. |
 
> **Observação:** as relações acima devem ser apresentadas como relações de segurança e não como prova de que cada alerta do ZAP demonstra diretamente a exploração dos riscos da Etapa 2.
 
---
 
## 6. Limitações e falsos positivos
 
**Responsável:** Renata
 
A verificação foi realizada em uma instância local do OWASP Juice Shop, uma aplicação deliberadamente vulnerável utilizada para fins educacionais.
 
Portanto, os resultados não devem ser interpretados como uma avaliação de uma aplicação de produção.
 
A análise realizada pelo ZAP também possui limitações. Alguns alertas identificam configurações potencialmente inseguras, mas não necessariamente comprovam que uma exploração completa seja possível.
 
Durante a execução foram identificados:
 
- **4** alertas de prioridade média
- **5** alertas de baixa prioridade
- **2** alertas informacionais
Os três achados selecionados para análise nesta etapa foram aqueles para os quais foram obtidas evidências e informações suficientes durante a execução.
 
---
 
## 7. Conclusão da verificação
 
**Responsável:** Renata
 
A verificação realizada com o OWASP ZAP permitiu identificar diferentes configurações e condições de segurança no OWASP Juice Shop executado localmente.
 
Entre os resultados analisados destacaram-se a configuração incorreta de CORS, a ausência do cabeçalho Content Security Policy (CSP) e a presença de identificador de sessão em URL.
 
Os achados foram classificados pelo ZAP como de prioridade média, com confiança alta nas evidências apresentadas pela ferramenta.
 
As principais medidas propostas são:
 
- Restringir corretamente as origens permitidas pelo CORS;
- Configurar uma política CSP adequada;
- Evitar a exposição de identificadores de sessão diretamente em URLs.
A principal limitação da verificação é que ela foi realizada em um ambiente local e educacional, não representando necessariamente o comportamento de um ambiente de produção.
 
Os resultados obtidos servem como evidência para a análise de segurança do projeto e podem orientar as próximas etapas, especialmente a definição de controles e correções de segurança.
 
---
 
## 8. Organização das evidências
 
**Responsável:** Renata
 
Os arquivos referentes à execução permanecem em:
 
```
evidencias/etapa-5/
```
 
Organização:
 
```
evidencias/
└── etapa-5/
    ├── zap-execucao.png
    ├── a01-cors.png
    ├── a02-csp.png
    ├── a03-session-id-url.png
    └── relatorio-da-verificacao.md
```
 
Os nomes são apenas uma sugestão; você pode manter os nomes que já utilizou.
 
---
 
## 9. Responsabilidade na Etapa 5

  
| Integrante | Responsabilidade |
|---|---|
| Renata | Preparação do ambiente, execução do OWASP Juice Shop, execução/verificação com ZAP, coleta das evidências, análise dos achados A01, A02 e A03, relação com os riscos anteriores, limitações e conclusão |
 
