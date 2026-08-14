# Entregável 1 — Modelagem de ameaças e Casos de Abuso

Sistema: **RapidoFood** (aplicativo de delivery). Ver a identificação e a
descrição no [README.md](README.md) e os usuários/ativos no
[Passo 1](<Passo 1 - Usuários, ativos e pontos de interação.md>).

## 5. Modelagem de ameaças com STRIDE

| ID | Categoria STRIDE | Componente ou ativo | Ameaça identificada | Possível impacto |
| --- | --- | --- | --- | --- |
| T01 | Spoofing | Conta do usuário | Um atacante utiliza credenciais roubadas (vazamento de outro serviço, phishing ou credential stuffing) para acessar a conta de outra pessoa, já que o login não exige múltiplo fator de autenticação. | Acesso a dados pessoais e endereços, realização de pedidos fraudulentos e uso do saldo/cartão salvo da vítima. |
| T02 | Tampering | Carrinho de Compras / API de Pedidos | Um cliente mal-intencionado intercepta e altera a requisição HTTP do pedido (ex.: utilizando proxy como Burp Suite) para modificar o preço unitário dos itens ou zerar o valor da taxa de entrega antes de enviar ao servidor. | Prejuízo financeiro direto para os restaurantes e para a plataforma, inconsistência no fluxo financeiro e registros de pedidos com valores incorretos. |
| T03 | Repudiation | Logs de Transação e Confirmação de Entrega | Um entregador alega ter realizado a entrega do pedido sem de fato ter entregado, ou um cliente nega ter recebido o pedido para solicitar reembolso indevido, em um cenário sem geração de código de validação OTP ou registros/logs auditáveis com timestamp. | Dificuldade no rastreamento e na resolução de disputas financeiras, prejuízos com reembolsos indevidos e perda de confiabilidade nas operações da plataforma. |
| T04 | Information Disclosure | API de pedidos | A consulta de um pedido devolve os dados a partir do identificador enviado na requisição, sem verificar se aquele pedido pertence ao usuário autenticado (IDOR / falha de controle de acesso a nível de objeto). Trocando o identificador, um cliente comum passa a visualizar pedidos de outras pessoas. | Exposição de nome, telefone, endereço de entrega, itens e valores de terceiros, viabilizando golpes direcionados e a coleta de dados pessoais em massa. |
| T05 | Denial of Service | API de pedidos | Um atacante envia um grande volume de requisições simultâneas para a API responsável pelo processamento de pedidos, consumindo os recursos do servidor e impedindo que novas solicitações legítimas sejam atendidas. | Clientes não conseguem realizar pedidos, restaurantes deixam de receber novas solicitações e ocorre indisponibilidade parcial do serviço. |
| T06 | Denial of Service | Servidor de autenticação | Um atacante realiza milhares de tentativas simultâneas de login, sobrecarregando o servidor de autenticação e impedindo que usuários legítimos acessem suas contas. | Clientes, restaurantes e entregadores ficam impossibilitados de acessar o aplicativo, interrompendo o funcionamento da plataforma. |
| T07 | Spoofing | Localização do entregador | Um entregador mal-intencionado falsifica o sinal de GPS (GPS spoofing) para simular que está em um local ou que concluiu uma entrega sem tê-la realizado. | Confirmação de entregas falsas, prejuízo a clientes e restaurantes e fraude no repasse de valores. |
| T08 | Elevation of Privilege | Controle de acesso da API | Um usuário comum manipula o identificador de papel/perfil na requisição (ex.: campo `role`) ou acessa um endpoint administrativo sem verificação de autorização (IDOR/broken access control), obtendo permissões de administrador. | Acesso ao painel administrativo, aos dados de todos os usuários e à possibilidade de alterar cadastros e pedidos. |
| T09 | Elevation of Privilege | Perfil de entregador/restaurante | Um entregador ou restaurante explora falhas de autorização para executar ações restritas a outro perfil (ex.: cancelar pedidos de terceiros ou editar cardápios de outra loja). | Manipulação indevida de pedidos e cardápios, prejuízo financeiro e quebra da separação de responsabilidades entre perfis. |
| T10 | Information Disclosure | Aplicativo do entregador | Os dados completos do cliente (nome, telefone e endereço) continuam visíveis no aplicativo do entregador mesmo depois de a entrega ter sido concluída, sem mascaramento dos dados nem prazo de expiração do acesso. | Uso dos dados fora da relação de entrega, possibilitando contato não solicitado, perseguição e assédio ao cliente, além de violação da finalidade prevista na LGPD. |
| T11 | Tampering | Status do Pedido / API | Um cliente mal-intencionado descobre o endpoint de atualização de status e força o cancelamento do pedido quando ele já está a caminho, explorando uma falha de autorização. | O restaurante perde os insumos, a plataforma processa um estorno financeiro indevido e o cliente recebe a comida de graça. |

> **Dica:** espera-se pelo menos uma ameaça por categoria. Todas as seis
> categorias do STRIDE devem ser cobertas ao final.

### 5.1 Interpretação da análise

As ameaças mostram que diferentes partes do RapidoFood precisam ser protegidas.
As **contas e credenciais** estão relacionadas à identidade dos usuários
(Spoofing); os **pedidos e valores** dependem da integridade dos dados
(Tampering); os **logs** permitem responsabilizar os autores das operações
(Repudiation); os **dados pessoais, de pagamento e de localização** exigem
confidencialidade (Information Disclosure); o serviço precisa permanecer
**disponível**, sobretudo em horários de pico (Denial of Service); e as
**funções administrativas** devem ser acessíveis somente por usuários
autorizados (Elevation of Privilege).

## 6. Casos de abuso

> Visão geral: [Diagrama de casos de abuso](<diagramas/casos-de-abuso.md>)
> (atores maliciosos, casos CA01–CA04 e categorias STRIDE envolvidas).

### CA01 — Sequestro de conta e escalonamento para acesso administrativo

**Ator:** atacante externo (usuário mal-intencionado).

**Objetivo:** assumir a conta de outro usuário e, a partir dela, obter
privilégios de administrador na plataforma.

**Condições necessárias:**

- o login não exige múltiplo fator de autenticação (MFA);
- não há limite de tentativas de login (sem rate limiting);
- existe uma falha de controle de acesso que permite acessar funções
  administrativas sem a devida verificação de permissão.

**Fluxo de abuso:**

1. O atacante obtém uma lista de e-mails e senhas vazados de outro serviço.
2. Automatiza tentativas de login no RapidoFood (credential stuffing), sem ser
   bloqueado por não haver limite de tentativas nem MFA.
3. Consegue acessar a conta de uma vítima e visualiza dados pessoais, endereços
   e histórico de pedidos.
4. Manipula a requisição (por exemplo, alterando o campo de perfil/`role`) ou
   acessa diretamente um endpoint administrativo que não valida a permissão.
5. Passa a ter acesso ao painel administrativo, com visão sobre os dados de
   outros usuários e capacidade de alterar cadastros e pedidos.

**Impacto esperado:** exposição em massa de dados pessoais, realização de
operações fraudulentas, perda de confiança na plataforma e possível controle
administrativo indevido do sistema.

**Categorias STRIDE relacionadas:** Spoofing, Elevation of Privilege e
Information Disclosure.

---

### CA02 — Manipulação de valores no carrinho de compras e falsificação de pagamento

**Ator:** cliente mal-intencionado (usuário da plataforma).

**Objetivo:** adquirir refeições e produtos pagando um valor significativamente menor do que o estipulado pelo restaurante ou com taxa de entrega zerada.

**Condições necessárias:**
- O aplicativo móvel/web valida as regras de negócio ou calcula o total do pedido predominantemente no lado do cliente (frontend);
- A API de pedidos aceita os valores enviados no corpo da requisição sem revalidá-los contra o banco de dados do cardápio oficial;
- Ausência de assinatura digital ou verificação de integridade no payload da transação.

**Fluxo de abuso:**
1. O atacante adiciona itens de alto valor ao carrinho de compras no aplicativo.
2. Antes de finalizar o pedido, ativa uma ferramenta de interceptação de tráfego HTTP/HTTPS (ex.: Burp Suite).
3. O atacante clica em "Finalizar Pedido" e intercepta a requisição `POST /api/v1/pedidos`.
4. Altera o parâmetro `preco_unitario` de R$ 80,00 para R$ 1,00 ou o campo `taxa_entrega` para R$ 0,00 diretamente no JSON.
5. Envia a requisição alterada para o servidor.
6. A API processa o pedido aceitando os valores modificados e encaminha a cobrança reduzida ao gateway de pagamento.
7. O pagamento é aprovado no valor adulterado e o restaurante recebe a notificação para preparar a refeição.

**Impacto esperado:** prejuízo financeiro direto ao restaurante e à plataforma, quebra de integridade dos dados das transações e perda da confiabilidade nas regras do sistema.

**Categorias STRIDE relacionadas:** Tampering e Elevation of Privilege.

---

### CA03 — Coleta em massa de dados pessoais por falha de autorização

**Ator:** cliente legítimo mal-intencionado (já cadastrado e autenticado na
plataforma).

**Objetivo:** obter, em grande escala, os dados pessoais e os endereços de
outros clientes do RapidoFood, para revenda ou para aplicar golpes
direcionados.

**Condições necessárias:**

- os pedidos são identificados por códigos sequenciais e previsíveis;
- a API devolve os dados de um pedido sem verificar se ele pertence ao usuário
  autenticado (falha de controle de acesso a nível de objeto);
- não há limite de requisições por conta (sem rate limiting);
- não há monitoramento capaz de detectar um volume anormal de consultas.

**Fluxo de abuso:**

1. O atacante cria uma conta comum no aplicativo e realiza um pedido verdadeiro,
   sem levantar suspeita.
2. Ao acompanhar o próprio pedido, observa que a requisição identifica o pedido
   por um código sequencial.
3. Substitui esse código pelo de outro pedido e percebe que o sistema devolve os
   dados normalmente, mesmo o pedido não sendo dele.
4. Automatiza a consulta, percorrendo faixas inteiras de códigos e armazenando
   as respostas.
5. Monta uma base com nome, telefone, endereço de entrega e histórico de consumo
   de milhares de clientes, sem que a plataforma registre ou bloqueie a
   atividade.

**Impacto esperado:** vazamento em massa de dados pessoais, exposição dos
clientes a golpes e a riscos à integridade física, violação da Lei Geral de
Proteção de Dados (LGPD) com risco de sanção da ANPD, além de dano à reputação
da plataforma.

**Categorias STRIDE relacionadas:** Information Disclosure e Elevation of
Privilege.

---

### CA04 — Ataque de negação de serviço ao aplicativo de delivery

**Ator:** atacante externo utilizando ferramentas automatizadas (bots).

**Objetivo:** tornar o aplicativo indisponível, impedindo que clientes,
restaurantes e entregadores utilizem seus serviços.

**Condições necessárias:**

- o aplicativo está acessível pela internet;
- não há mecanismos suficientes para limitar ou bloquear um grande volume de
  requisições simultâneas.

**Fluxo de abuso:**

1. O atacante utiliza ferramentas automatizadas para enviar milhares de
   requisições simultaneamente ao aplicativo.
2. O servidor recebe um volume excessivo de solicitações e seus recursos ficam
   sobrecarregados.
3. Clientes, restaurantes e entregadores enfrentam lentidão ou ficam
   impossibilitados de acessar e utilizar o sistema.

**Impacto esperado:** interrupção parcial ou total do serviço, perda de pedidos,
prejuízos financeiros para restaurantes e para a plataforma, além da
insatisfação dos usuários.

**Categorias STRIDE relacionadas:** Denial of Service.

---

### CA05 — Entregas falsas por falsificação de localização (GPS spoofing)

**Ator:** entregador mal-intencionado (usuário legítimo com má-fé).

**Objetivo:** receber o pagamento por corridas sem realizar a entrega,
simulando deslocamento e conclusão do pedido por meio da falsificação do sinal
de GPS do dispositivo.

**Condições necessárias:**

- o aplicativo confia exclusivamente na localização informada pelo dispositivo
  do entregador, sem validação cruzada;
- não há confirmação de entrega pelo cliente (por exemplo, código OTP) nem
  verificação de coerência entre a rota percorrida e o tempo de deslocamento;
- ferramentas de *fake GPS* conseguem alterar a localização reportada pelo
  aparelho.

**Fluxo de abuso:**

1. O entregador aceita uma corrida e recebe o endereço de entrega.
2. Em vez de se deslocar, utiliza um aplicativo de *fake GPS* para simular que
   está percorrendo a rota até o endereço do cliente.
3. O aplicativo atualiza a localização falsa e considera que o entregador chegou
   ao destino.
4. O entregador marca o pedido como "entregue" sem que o cliente tenha recebido.
5. A plataforma registra a entrega como concluída e libera o repasse do valor ao
   entregador.

**Impacto esperado:** prejuízo financeiro ao cliente (que paga sem receber) e à
plataforma (repasse indevido e reembolsos), fraude recorrente, disputas de
difícil resolução e perda de confiança no serviço.

**Categorias STRIDE relacionadas:** Spoofing e Repudiation. _(Relaciona-se à
ameaça T07 da tabela STRIDE.)_

---

### CA06 — Fraude de cupons com múltiplas contas

**Ator:** usuário mal-intencionado (cliente da plataforma).

**Objetivo:** utilizar repetidamente cupons de "primeiro pedido" ou promoções destinadas a novos usuários, obtendo descontos indevidos.

**Condições necessárias:**

- o sistema concede cupons de boas-vindas apenas com base na criação de uma nova conta;
- não há validação da identidade do usuário por CPF, cartão de pagamento, dispositivo ou outro identificador confiável;
- não existe mecanismo para detectar a criação excessiva de contas pelo mesmo usuário.

**Fluxo de abuso:**

1. O atacante cria uma nova conta utilizando outro endereço de e-mail ou número de telefone.
2. O sistema libera automaticamente um cupom destinado ao primeiro pedido.
3. O atacante realiza uma compra utilizando o desconto promocional.
4. Após utilizar o cupom, cria outra conta e repete o processo diversas vezes.
5. O abuso continua até que o sistema identifique a fraude ou os cupons sejam esgotados.

**Impacto esperado:** prejuízo financeiro para a plataforma e para os restaurantes participantes das promoções, uso indevido de campanhas promocionais, distorção dos indicadores de novos usuários e redução da efetividade das ações de marketing.

**Categorias STRIDE relacionadas:** Spoofing e Repudiation.
---

### CA07 — Cadastro de restaurante ou entregador falso para coleta de dados e fraude

**Ator:** atacante externo (falso parceiro ou falso prestador de serviço).

**Objetivo:** obter acesso ilícito a dados pessoais e de localização dos clientes ou receber repasses financeiros por pedidos que nunca serão entregues.

**Condições necessárias:**

- o processo de onboarding e cadastro de parceiros (restaurantes ou entregadores) permite a aprovação automática ou sem validação documental rigorosa (sem checagem de CNPJ, CPF, CNH ou fotos reais);
- o aplicativo libera o endereço completo e telefone do cliente assim que o pedido é aceito pelo parceiro falso.

**Fluxo de abuso:**

1. O atacante cria um perfil falso de restaurante (com cardápio e preços fictícios) ou de entregador utilizando dados/documentos gerados ou de terceiros.
2. O sistema aprova o cadastro sem verificar a veracidade da documentação ou a existência física do estabelecimento/entregador.
3. No caso de restaurante falso, o atacante recebe pedidos de clientes legítimos e obtém nome, telefone e endereço completo de entrega.
4. No caso de entregador falso, o atacante aceita corridas apenas para ter acesso à localização exata e aos dados de contato da vítima.
5. O atacante não prepara nem entrega os pedidos, utilizando os dados obtidos para aplicar golpes externos (ex.: cobranças falsas via PIX por fora da plataforma, engenharia social ou perseguição).

**Impacto esperado:** violação grave da privacidade e segurança física dos clientes, exposição de dados pessoais (PII), fraude financeira e grave dano à reputação da plataforma.

**Categorias STRIDE relacionadas:** Spoofing, Information Disclosure e Elevation of Privilege.

---

### CA08 — Manipulação de avaliações e reputação de restaurantes

**Ator:** restaurante concorrente ou usuário mal-intencionado (comprador de notas/avaliador falso).

**Objetivo:** inflar artificialmente a nota de reputação do seu próprio estabelecimento ou difamar e derrubar a nota de restaurantes concorrentes no aplicativo.

**Condições necessárias:**

- o endpoint de submissão de avaliações e notas não valida se o usuário realmente realizou e concluiu um pedido no estabelecimento avaliado;
- ausência de validação de limite de frequência ou de detecção de comportamentos anômalos (ex.: criação de contas em massa para avaliar o mesmo local).

**Fluxo de abuso:**

1. O atacante cria diversas contas falsas (ou utiliza automações de botnet) na plataforma.
2. Sem precisar efetuar a compra de uma refeição real, envia requisições diretamente para a API de avaliações.
3. Para beneficiar seu próprio restaurante, envia consecutivas notas máximas (5 estrelas) acompanhadas de comentários elogiadores.
4. Para prejudicar um concorrente direto, dispara uma onda de avaliações negativas (1 estrela) com relatos falsos sobre a qualidade da comida ou atrasos na entrega.
5. O sistema recalcula a nota média dos restaurantes afetados sem verificar a legitimidade das transações.

**Impacto esperado:** quebra de integridade no sistema de reputação da plataforma, indução de clientes ao erro, concorrência desleal, prejuízo financeiro e perda de vendas para estabelecimentos legítimos.

**Categorias STRIDE relacionadas:** Tampering, Repudiation e Spoofing.

---

### CA09 — Alteração indevida do status do pedido para fraude de estorno

**Ator:** cliente mal-intencionado (com conhecimentos técnicos básicos).

**Objetivo:** Forçar o cancelamento de um pedido que já está em trânsito para acionar o sistema de estorno automático, recebendo a refeição de graça.

**Condições necessárias:**
- A API de atualização de status do pedido (ex: `PUT /api/v1/pedidos/{id}/status`) não restringe a ação estritamente aos perfis de "Restaurante" ou "Administrador".
- O sistema possui uma regra de negócio que estorna automaticamente pagamentos de pedidos cancelados antes da entrega.

**Fluxo de abuso:**
1. O atacante realiza um pedido legítimo e aguarda o restaurante aceitar e despachar a refeição com o entregador.
2. Utilizando uma ferramenta de interceptação de requisições (ex: Burp Suite ou o próprio DevTools do navegador), o atacante descobre o formato da requisição que atualiza o status do pedido.
3. Ele envia uma requisição manual para o servidor alterando o campo `status` do seu pedido para `CANCELADO`.
4. O backend, falhando em validar o perfil do autor da requisição, acata a mudança.
5. O sistema de pagamentos é acionado automaticamente e devolve o dinheiro ao cartão do atacante.
6. O entregador, que já estava na rua e pode estar sem sinal/atualização em tempo real, chega ao destino e entrega a comida.

**Impacto esperado:** Prejuízo financeiro direto e integral para o restaurante (perda de insumos e taxa de entrega) e para a plataforma (estorno bancário via cartão de crédito).

**Categorias STRIDE relacionadas:** Tampering (modificação do status) e Elevation of Privilege (ação executada por perfil sem permissão).

## 7. Considerações finais

**Ameaças mais preocupantes:** acesso indevido a contas de usuários, alteração de pedidos e valores, obtenção de privilégios administrativos, exposição de dados pessoais e indisponibilidade do sistema causada por ataques de negação de serviço.

**Ativos mais importantes:** credenciais de acesso, dados pessoais, dados de pagamento, integridade dos pedidos, localização em tempo real, registros de auditoria (logs) e disponibilidade da plataforma.

**Tipos de abuso de maior impacto:** sequestro de contas, manipulação de pedidos, coleta indevida de dados pessoais, ataques de negação de serviço, fraudes com cupons, falsificação de localização por entregadores, cadastro de parceiros falsos e manipulação de avaliações.

**Principais dificuldades encontradas pelo grupo:** identificar ameaças específicas para cada categoria do STRIDE, diferenciar ameaças de casos de abuso, relacionar corretamente cada caso às categorias do STRIDE e manter a consistência entre os casos de abuso, a modelagem de ameaças e os diagramas.

**Possíveis medidas de proteção:** utilização de autenticação multifator (MFA), limitação de tentativas de login, validação de autorização em todas as operações, criptografia de dados sensíveis, registros de auditoria, validação documental de parceiros, confirmação de entrega por código OTP, monitoramento de atividades suspeitas e mecanismos de proteção contra ataques de negação de serviço.
