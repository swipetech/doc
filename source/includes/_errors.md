# Tratamento de Erros

Um [Erro](#error) possui alguns campos que permitem identificar a causa do problema. 

## code

Através do `code` é possível identificar o grupo a qual o Erro pertence. São estes os grupos:

- `validation_error`: Erro de validação. Significa que algúm valor passado é inválido. 
Neste caso, cada Sub-Erro possui `field` com o nome do campo inválido.

- `too_many_requests`: Limite de requests excedido.

- `unauthorized`: Não autorizado. Representa algúm problema de autenticação com a API.
Verifique sua `Api Key` e `Secret`.

- `resource_not_found`: Recurso não encontrado.

- `too_many_accounts`: Limite de Contas filhas excedido.

- `max_ops_limit_exceeded`: Limite de Operações em um Pagamento excedido.

- `action_error`: Erro ao executar a Ação.

- `insecure_connection`: Conexão insegura. (É obrigatório que todas as chamadas utilizem `https`)

- `payment_failed`: Pagamento falhou. (Dica: utilize o campo `op_code` de cada Operação dentro do Pagamento para mais detalhes)

- `internal_server_error`: Erro interno da aplicação.

## msg

É uma mensagem legível sobre a causa do problema.

## sub_errors

[`sub_errors`](#suberror) contém uma lista de Sub-Erros com detalhes específicos sobre a causa do problema. 
Assim como o Error, ele possui um campo `code`, `msg` (e `field` em caso de Erro de validação):

- `pay_id_conflict`: O remetente e destinatário devem ser diferentes.

- `pay_from_is_empty`: Remetente vazio.

- `pay_to_is_empty`: Destinatário vazio.

- `pay_asset_id_is_empty`: ID do Ativo vazio. 

- `pay_invalid_amount`: Valor de Operação inválido.

- `pay_asset_not_found`: Ativo não encontrado.

- `pay_from_not_found`: Remetente não encontrado.

- `pay_to_not_found`: Destinatário não encontrado.
