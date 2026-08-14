# Prática 1: Validação Server-Side de Valores do Pedido

**Objetivo:** Mitigar o risco **R04 (Tampering - Alteração de valores no carrinho e pedido)**.  
**Responsável:** Andre Luiz Montanha de Abreu

Esta implementação (em formato de pseudocódigo) demonstra a aplicação do princípio de não confiar nos dados do cliente (*Client-Side Enforcement of Server-Side Security*). 

O código garante que o backend não utilize os valores monetários enviados pelo aplicativo. O sistema recalcula obrigatoriamente o total da compra consultando os preços originais e invioláveis diretamente no banco de dados e as taxas no serviço de logística.

## Pseudocódigo: Controlador de Checkout (`API de Pedidos`)

```python
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
        // Registra a tentativa de fraude/adulteração nos logs de auditoria
        Logger.registrar(
            nivel: "CRITICO", 
            evento: "Adulteração de valores no carrinho detectada", 
            usuario: usuario_id, 
            valor_recebido: payload.valor_total, 
            valor_real: valor_total_calculado_servidor
        )
        // Bloqueia a transação antes de enviar ao Gateway de Pagamento
        retornar ErroHTTP(400, "Inconsistência nos valores do pedido. Operação abortada.")
        
    // 5. Se os valores baterem perfeitamente, segue o fluxo normal
    pedido_novo = DB.Pedidos.criar(usuario_id, payload.itens, valor_total_calculado_servidor)
    GatewayPagamento.processar(pedido_novo)
    
    retornar SucessoHTTP(201, "Pedido criado com sucesso", pedido_novo)
```
