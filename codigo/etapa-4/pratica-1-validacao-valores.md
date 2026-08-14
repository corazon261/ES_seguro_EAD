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
