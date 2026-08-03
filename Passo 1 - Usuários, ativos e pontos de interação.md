## 3. Usuários, ativos e pontos de interação

> **Responsável:** Gustavo (revisão e expansão desta seção). O rascunho abaixo já
> traz a estrutura e o conteúdo base do sistema de delivery — sinta-se à vontade
> para ajustar, detalhar ou acrescentar itens.

### 3.1 Usuários e perfis

| Usuário ou perfil | Principais ações |
| --- | --- |
| Cliente | Consultar restaurantes e produtos, montar carrinho, realizar pedidos, pagar, acompanhar a entrega e avaliar |
| Restaurante / Loja | Cadastrar e editar o cardápio, receber e aceitar pedidos, atualizar o status de preparo |
| Entregador | Aceitar corridas, visualizar o endereço de entrega, atualizar a localização e confirmar a entrega |
| Administrador | Gerenciar contas e permissões, moderar cadastros, resolver disputas e administrar a plataforma |

### 3.2 Ativos importantes

São considerados ativos importantes os recursos que podem causar prejuízo caso
sejam acessados, alterados, destruídos ou indisponibilizados indevidamente:

- credenciais de acesso (contas de clientes, restaurantes, entregadores e administradores);
- dados pessoais e de contato (nome, e-mail, telefone, endereços de entrega);
- dados de pagamento (cartões salvos, transações);
- localização em tempo real de clientes e entregadores;
- pedidos e seus valores;
- histórico de pedidos e de consumo;
- avaliações e notas;
- mensagens trocadas no chat cliente↔entregador;
- permissões e papéis dos usuários;
- registros e logs das operações;
- disponibilidade do serviço, especialmente em horários de pico.

### 3.3 Pontos de interação e componentes

| Elemento | Função |
| --- | --- |
| Aplicativo móvel / Portal web | Interface utilizada por clientes, restaurantes e entregadores |
| Serviço de autenticação | Valida a identidade e as credenciais dos usuários |
| Aplicação / API | Processa as regras de negócio e as operações do sistema |
| Banco de dados | Armazena contas, pedidos, dados pessoais, pagamentos e logs |
| Gateway de pagamento (serviço externo) | Processa as transações financeiras |
| Serviço de mapas / geolocalização (serviço externo) | Fornece rotas e a localização em tempo real |
| Serviço de notificações | Envia avisos sobre o status dos pedidos e entregas |
