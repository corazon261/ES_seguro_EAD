# Etapa 4 — Código Seguro e Testes de Segurança

**Sistema:** RapidoFood (app de delivery)

Esta etapa dá continuidade aos riscos, requisitos de segurança e decisões de
arquitetura definidos nas etapas anteriores.

O objetivo é demonstrar como as decisões da arquitetura podem ser
transformadas em práticas de implementação segura.

Não será necessário implementar o sistema completo. O grupo poderá utilizar
código, pseudocódigo, trechos ilustrativos, fluxos, configurações comentadas
ou descrições detalhadas de como a implementação seria realizada.

---

## 1. Prática de código seguro 1

**Responsável principal: Gustavo**

A primeira prática deverá estar relacionada aos riscos e requisitos de
segurança identificados nas etapas anteriores.

### 1.1 Risco e requisito relacionados


| Campo | Conteúdo |
|---|---|
| Risco relacionado | **R04 — Alteração de valores no carrinho e pedido (Tampering)** (Alto, 9), originado da ameaça T02 e do caso de abuso CA02. |
| Requisito relacionado | O backend (`API Gateway`/`Motor de Pedidos`) deve recalcular obrigatoriamente o preço total de todos os itens e taxas no lado do servidor antes de enviar a transação ao gateway de pagamento, rejeitando payloads com divergência de valores. |
| Problema de segurança tratado | A aplicação confiava nos valores monetários (como preço unitário ou taxa de entrega) enviados no payload da requisição pelo cliente. Um atacante usando ferramentas de interceptação (ex: Burp Suite) podia adulterar os valores para realizar compras por centavos ou com desconto indevido. |

### 1.2 Referência da OWASP

**Responsável: Renata**

| Campo | Conteúdo |
|---|---|
| Referência utilizada | **OWASP Input Validation Cheat Sheet** |
| Relação da referência com a prática | A referência recomenda que dados recebidos de fontes não confiáveis sejam validados no lado do servidor antes de serem processados. Também destaca a importância da validação semântica, verificando se os valores recebidos são coerentes com as regras do sistema. Essa orientação se relaciona diretamente ao R04, pois o backend do RapidoFood deve recalcular e validar os preços, taxas e valores da transação sem confiar nos valores enviados pelo cliente. |

### 1.3 Testes definidos antes da implementação

Os testes foram definidos antes da apresentação da implementação para garantir a verificação dos limites do controle.

| ID | Entrada ou ação | Tipo de teste | Resultado seguro esperado |
|---|---|---|---|
| TS01 | Cliente realiza requisição `POST /api/v1/pedidos/checkout` enviando itens válidos e o valor total recalculado corretamente de acordo com a tabela do banco de dados. | Caso de uso válido | A API revalida todos os preços no servidor, confirma que o valor calculado pelo backend é idêntico ao do payload, cria o pedido com status `PENDENTE` e encaminha a transação ao gateway de pagamento (`201 Created`). |
| TS02 | Cliente intercepta a requisição e altera o campo `preco_unitario` de R$ 50,00 para R$ 1,00 no JSON enviado para a API. | Caso malicioso, inválido ou não autorizado | O backend identifica a divergência entre o valor enviado no payload e o valor real do catálogo no banco de dados. A requisição é rejeitada com erro `400 Bad Request`, o pedido não é criado e o evento de adulteração é registrado nos logs de segurança. |

### 1.4 Implementação, pseudocódigo ou descrição

**Responsável: Andre**

Abaixo apresentamos o pseudocódigo da função responsável por processar o fechamento do pedido no backend (`API de Pedidos`). O código demonstra a validação estrita no lado do servidor (*server-side*), ignorando os preços unitários confiados pelo cliente e recalculando tudo com base em consultas seguras ao Banco de Dados.

```python
// Pseudocódigo: Controlador de Checkout de Pedidos (Backend)
funcao processarCheckout(Requisicao req):
    payload = req.corpo
    usuario_id = req.token_sessao.usuario_id
    
    // 1. Variável para armazenar o valor real calculado no servidor
    valor_total_calculado_servidor = 0.00
    
    // 2. Itera sobre os itens enviados no carrinho do cliente
    para cada item em payload.itens:
        // Consulta o preço real e atualizado diretamente do Banco de Dados
        produto_banco = DB.consultar("SELECT preco FROM Produtos WHERE id = ?", item.id)
        
        se produto_banco nao existir:
            retornar ErroHTTP(404, "Produto não encontrado")
            
        // Soma ao total o (Preço Seguro do DB * Quantidade)
        valor_total_calculado_servidor += (produto_banco.preco * item.quantidade)
        
    // 3. Consulta a taxa de entrega real calculada pelo sistema de logística
    taxa_entrega_real = Logistica.calcularTaxa(usuario_id, payload.restaurante_id)
    valor_total_calculado_servidor += taxa_entrega_real
    
    // 4. VALIDAÇÃO DE SEGURANÇA (Mitigação do Risco R04 - Tampering)
    // Compara o valor final que o cliente diz que vai pagar com o valor real do servidor
    se payload.valor_total != valor_total_calculado_servidor:
        // Registra a tentativa de fraude/adulteração
        Logger.registrar(
            nivel: "CRITICO", 
            evento: "Adulteração de valores no carrinho detectada", 
            usuario: usuario_id, 
            valor_recebido: payload.valor_total, 
            valor_real: valor_total_calculado_servidor
        )
        // Bloqueia a transação antes de enviar ao Gateway de Pagamento
        retornar ErroHTTP(400, "Inconsistência nos valores do pedido. Operação abortada.")
        
    // 5. Se os valores baterem, segue o fluxo normal
    pedido_novo = DB.Pedidos.criar(usuario_id, payload.itens, valor_total_calculado_servidor)
    GatewayPagamento.processar(pedido_novo)
    
    retornar SucessoHTTP(201, "Pedido criado com sucesso", pedido_novo)
```

### 1.5 Resultado esperado

**Responsável: Andre**

*(preencher — Andre)*

---

## 2. Prática de código seguro 2

**Responsável principal: Luiz**

A segunda prática deverá estar relacionada aos riscos e requisitos de
segurança identificados nas etapas anteriores.

### 2.1 Risco e requisito relacionados

| Campo | Conteúdo |
|---|---|
| Risco relacionado | **R06 — Exposição de dados de terceiros por falha de autorização** (Crítico, 12), originado da ameaça T04 e do caso de abuso CA03. |
| Requisito relacionado | Todo endpoint que devolve dados de um pedido deve confirmar, no servidor, que o pedido pertence ao usuário autenticado antes de responder. |
| Problema de segurança tratado | A API identifica o pedido apenas pelo valor recebido na requisição e confia nesse valor. Como não compara o dono do pedido com o usuário do token, qualquer cliente autenticado lê pedidos alheios trocando o identificador — falha de autorização a nível de objeto (IDOR). |

### 2.2 Referência da OWASP

**Responsável: Renata**

| Campo | Conteúdo |
|---|---|
| Referência utilizada | **OWASP Top 10:2021 — A01: Broken Access Control** |
| Relação da referência com a prática | A referência trata de falhas de controle de acesso que permitem que usuários atuem fora das permissões previstas. A prática 2 aplica esse princípio ao acesso aos pedidos, exigindo que o backend verifique se o pedido pertence ao usuário autenticado antes de retornar seus dados. Dessa forma, a alteração do identificador do pedido não deve permitir o acesso a informações de terceiros. |

### 2.3 Testes definidos antes da implementação

**Responsável: Luiz**

Os testes deverão ser definidos antes da apresentação da implementação.

| ID | Entrada ou ação | Tipo de teste | Resultado seguro esperado |
|---|---|---|---|
| TS03 | Cliente autenticado consulta `GET /api/v1/pedidos/{id}` informando o identificador de um pedido **feito por ele mesmo** | Caso de uso válido | A API responde `200 OK` com os dados completos do pedido (itens, valores, endereço e status). O acesso é registrado no log de auditoria. |
| TS04 | O mesmo cliente autenticado repete a chamada trocando o identificador pelo de um pedido **de outro usuário** | Caso malicioso, inválido ou não autorizado | A API responde `403 Forbidden` sem devolver nenhum dado do pedido, nem mesmo em mensagem de erro. A tentativa é registrada com o usuário, o identificador solicitado e o horário, e alimenta o alerta de acesso indevido previsto no plano de tratamento do R06. |

> Observação sobre o TS04: a resposta não deve diferenciar "pedido inexistente"
> de "pedido de outro usuário". Se os códigos ou os tempos de resposta forem
> diferentes, o atacante consegue descobrir quais identificadores existem mesmo
> sem ler os dados, o que reabre parcialmente a enumeração descrita no CA03.

### 2.4 Implementação, pseudocódigo ou descrição

**Responsável: Lucas**

*(preencher — Lucas)*

### 2.5 Resultado esperado

**Responsável: Lucas**

*(preencher — Lucas)*

---

## 3. Materiais da implementação

Os materiais utilizados para demonstrar as duas práticas deverão ser
armazenados em:

`codigo/etapa-4/`

Poderão ser incluídos:

- código;
- pseudocódigo;
- trechos ilustrativos;
- fluxos;
- configurações comentadas;
- descrições detalhadas da implementação.

### Materiais da prática 1

**Responsável: Andre**

*(preencher nome do arquivo ou material produzido)*

### Materiais da prática 2

**Responsável: Lucas**

*(preencher nome do arquivo ou material produzido)*

---

## 4. Relação entre as práticas e as etapas anteriores

**Responsável: Renata**

As práticas de código seguro foram relacionadas aos riscos identificados na
Etapa 2, aos requisitos de segurança definidos na Etapa 3 e às respectivas
decisões de arquitetura.

| Prática | Risco relacionado | Requisito relacionado | Decisão de arquitetura relacionada |
|---|---|---|---|
| Prática 1 — Validação server-side dos valores do pedido | **R04 — Alteração de valores no carrinho e pedido (Tampering)** | O backend deve recalcular e validar os itens, preços e taxas do pedido antes do envio ao gateway de pagamento, sem confiar nos valores enviados pelo cliente. | **DA03 — Validação server-side dos valores do pedido** |
| Prática 2 — Autorização server-side para acesso a pedidos | **R06 — Exposição de dados de terceiros por falha de autorização (Information Disclosure)** | Todo endpoint que devolve dados de um pedido deve confirmar, no servidor, que o pedido pertence ao usuário autenticado antes de responder. | **DA02 — Autorização server-side para acesso a pedidos** |

A Prática 1 implementa o controle definido para o R04, garantindo que valores
financeiros sejam recalculados e validados pelo backend antes da transação ser
encaminhada ao gateway de pagamento.

A Prática 2 implementa o controle definido para o R06, garantindo que a
autorização seja verificada no servidor e impedindo que um usuário autenticado
acesse pedidos pertencentes a terceiros por meio da alteração do identificador
do pedido.

---

## 5. Organização final da Etapa 4

Antes da conclusão da etapa, o grupo deverá verificar se:

- foram selecionadas exatamente duas práticas de código seguro;
- as duas práticas estão relacionadas aos riscos e requisitos anteriores;
- cada prática possui dois testes;
- os testes foram definidos antes da implementação;
- cada prática possui um caso de uso válido;
- cada prática possui um caso malicioso, inválido ou não autorizado;
- foi apresentado código, pseudocódigo ou descrição detalhada da implementação;
- os resultados esperados estão definidos;
- as referências da OWASP utilizadas estão identificadas;
- os materiais estão armazenados em `codigo/etapa-4/`;
- a participação individual está demonstrada pelos commits.

---

## 6. Divisão da Etapa 4

| Integrante | Responsabilidade |
|---|---|
| **Gustavo** | Prática de código seguro 1, risco/requisito relacionados e dois testes |
| **Luiz** | Prática de código seguro 2, risco/requisito relacionados e dois testes |
| **Andre** | Implementação, pseudocódigo ou descrição da prática 1 e resultado esperado |
| **Lucas** | Implementação, pseudocódigo ou descrição da prática 2 e resultado esperado |
| **Renata** | Referências OWASP das duas práticas e relação das práticas com riscos, requisitos e decisões anteriores |

---

## Referências

**Responsável: Renata**

- OWASP. **Input Validation Cheat Sheet**. OWASP Cheat Sheet Series.
- OWASP. **OWASP Top 10:2021 — A01: Broken Access Control**.
