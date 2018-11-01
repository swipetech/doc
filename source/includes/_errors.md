# Tratamento de Erros

Um [Erro](#error) possui alguns campos que permitem identificar a causa do problema. 

## code

Através do `code` é possível identificar o grupo a qual o Erro pertence. A maioria deles possui um campo `sub_errors` com detalhes mais específicos. São estes os grupos:

- `internal_server_error`: Erro interno da aplicação.

- `bad_request`: Requisição mal construída. Normalmente acontece quando o body não corresponde ao formato esperado. 

- `not_found`: Recurso não encontrado.

- `unauthorized`: Não autorizado. Representa algúm problema de autenticação com a API.

- `validation_error`: Erro de validação. Significa que algúm valor passado é inválido. Neste caso, cada Sub-Erro possui `field` com o nome do campo inválido.

- `transfer_error`: Transferência falhou.

- `insecure_connection`: Conexão insegura. (É obrigatório que todas as chamadas utilizem `https`)

## msg

É uma mensagem legível sobre a causa do problema.

## sub_errors

[`sub_errors`](#suberror) contém uma lista de Sub-Erros com detalhes específicos sobre a causa do problema. 
Assim como o Error, ele possui um campo `code`, `msg` (e `field` em caso de Erro de validação):

### `validation_error`

- `transfer_from_is_empty`: Remetente da Transferência não pode ser vazio.

- `transfer_to_is_empty`: Destinatário da Transferência não pode ser vazio.

- `transfer_asset_id_is_empty`: Asset não pode ser vazio na Transferência.
 
- `transfer_invalid_amount`: Valor inválido para Operação dentro da Transferência. 

- `transfer_invalid_op_length`: Número inválido de Operações dentro de uma Transferência. 


### `transfer_error`

- `transfer_asset_not_found`: Ativo não encontrado.

- `transfer_from_not_found`: Remetente não encontrado.

- `transfer_to_not_found`: Destinatário não encontrado.

- `transfer_to_asset_not_supported`: Ativo não suportado pela Conta destinatária.

- `transfer_from_asset_not_supported`: Ativo não suportado pela Conta remetente.

- `op_underfunded`: Saldo insuficiente para Operação. 
