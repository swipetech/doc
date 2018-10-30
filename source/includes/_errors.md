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

- `transfer_failed`: Transferência falhou. (Dica: utilize o campo `op_code` de cada Operação dentro da Transferência para mais detalhes)

- `internal_server_error`: Erro interno da aplicação.

## msg

É uma mensagem legível sobre a causa do problema.

## sub_errors

[`sub_errors`](#suberror) contém uma lista de Sub-Erros com detalhes específicos sobre a causa do problema. 
Assim como o Error, ele possui um campo `code`, `msg` (e `field` em caso de Erro de validação):

- `transfer_id_conflict`: O remetente e destinatário devem ser diferentes.

- `transfer_from_is_empty`: Remetente vazio.

- `transfer_to_is_empty`: Destinatário vazio.

- `transfer_asset_id_is_empty`: ID do Ativo vazio. 

- `transfer_invalid_amount`: Valor de Operação inválido.

- `transfer_asset_not_found`: Ativo não encontrado.

- `transfer_from_not_found`: Remetente não encontrado.

- `transfer_to_not_found`: Destinatário não encontrado.
