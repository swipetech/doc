# Estruturas

## OrganizationResponse
Nome | Tipo | Descrição
---- | ---- | ---------
data | [OrganizationReceipt](#organizationreceipt) | Informações de uma Organização e um recibo de sua criação 
error | [Error](#error) |


## AccountResponse
Nome | Tipo | Descrição
---- | ---- | ---------
data | [AccountReceipt](#accountreceipt) | Informações de uma Conta e um recibo de sua criação
error | [Error](#error)


## AccountsResponse
Nome | Tipo | Descrição
---- | ---- | ---------
data | [[]AccountReceipt](#accountreceipt) | Lista de informações de Conta com recibo de sua criação
error | [Error](#error) 


## OrganizationReceipt
Nome | Tipo | Descrição
---- | ---- | ---------
receipt | [Receipt](#receipt) | Recibo da criação da Organização
organization | [Organization](#organization) | Informações de uma Organização


## AccountReceipt
Nome | Tipo | Descrição
---- | ---- | ---------
receipt | [Receipt](#receipt) | Recibo da criação da Conta
account | [Account](#account) | Informações de uma Conta


## Receipt
Nome | Tipo | Descrição
---- | ---- | ---------
id | string | ID do Recibo
created_at | string | Data de criação do Recibo


## Organization
Nome | Tipo | Descrição
---- | ---- | ---------
id | string | ID da Organização
name | string | Nome
balances | [[]Balance](#balance) | Lista de saldos da Organização para todos seus Ativos


## Account
Nome | Tipo | Descrição
---- | ---- | ---------
id | string | ID da Conta
balances | [[]Balance](#balance) | 


## Balance
Nome | Tipo | Descrição
---- | ---- | ---------
asset_code | string | código do Ativo
asset_id | string | ID do Ativo
balance | float64 | Saldo atual do Ativo


## Error
Nome | Tipo | Descrição
---- | ---- | ---------
code | string | Código que identifica a causa
msg | string | Mensagem traduzida
sub_errors | [[]SubError](#suberror) | Lista de sub-erros. Cada sub-erro possui informações mais detalhadas sobre a causa. 


## SubError
Nome | Tipo | Descrição
---- | ---- | ---------
code | string | Código que identifica a causa
msg | string | Mensagem traduzida
field | string | Campo com qual o erro se relaciona. (Somente no caso de [erro de validação](#erro-de-validacao))
