# Estruturas

## OrganizationResponse
Nome | Tipo | Descrição
---- | ---- | ---------
data | [OrganizationReceipt](#organizationreceipt) | Informações de uma Organização com um recibo de sua criação 
error | [Error](#error) |


## AccountResponse
Nome | Tipo | Descrição
---- | ---- | ---------
data | [AccountReceipt](#accountreceipt) | Informações de uma Conta com um recibo de sua criação
error | [Error](#error)


## AccountsResponse
Nome | Tipo | Descrição
---- | ---- | ---------
data | [[]AccountReceipt](#accountreceipt) | Lista de informações de Conta com um recibo de sua criação
error | [Error](#error) 


## AssetsResponse
Nome | Tipo | Descrição
---- | ---- | ---------
data | [[]AssetReceipt](#assetreceipt) | Informações de um Ativo com um recibo de sua criação
error | [Error](#error) | 


## PaymentsResponse
Nome | Tipo | Descrição
---- | ---- | ---------
data | [[]PaymentReceipt](#paymentreceipt) | Lista de informações sobre um Pagamento com um recibo de cada ocorrência
error | [Error](#error) | 


## PaymentResponse
Nome | Tipo | Descrição
---- | ---- | ---------
data | [PaymentReceipt](#paymentreceipt) | Informações sobre um Pagamento com um recibo de sua ocorrência
error | [Error](#error) | 


## OrganizationReceipt
Nome | Tipo | Descrição
---- | ---- | ---------
receipt | [Receipt](#receipt) | Recibo da criação da Organização
organization | [Organization](#organization) | Informações do Organização


## AccountReceipt
Nome | Tipo | Descrição
---- | ---- | ---------
receipt | [Receipt](#receipt) | Recibo da criação da Conta
account | [Account](#account) | Informações do Conta


## AssetReceipt
Nome | Tipo | Descrição
---- | ---- | ---------
receipt | [Receipt](#receipt) | Recibo da criação do Ativo
asset | [Asset](#asset) | Informações do Ativo


## PaymentReceipt
Nome | Tipo | Descrição
---- | ---- | ---------
receipt | [Receipt](#receipt) | Recibo da ocorrência do Pagamento
payment | [Payment](#payment) | Informações do Pagamento 


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
balances | [[]Balance](#balance) | Lista de saldos para todos os Ativos suportados pela Conta


## Asset
Nome | Tipo | Descrição
---- | ---- | ---------
id | string | ID do Ativo
code | string | Texto entre 4 e 12 caracteres que representa o Ativo
limit | float64 | Número máximo de unidades a ser emitido
 
 
## Payment
Nome | Tipo | Descrição
---- | ---- | ---------
id | string | ID do Pagamento
operations | [[]PaymentOperation](#paymentoperation) | Lista de operações incluídas no Pagamento


## PaymentOperation
Nome | Tipo | Descrição
---- | ---- | ---------
from | string | ID do remetente
to | string | ID do destinatário
amount | float64 | Valor a ser transferido
asset | string | ID do Ativo do Pagamento


## Balance
Nome | Tipo | Descrição
---- | ---- | ---------
asset_code | string | código do Ativo
asset_id | string | ID do Ativo
balance | float64 | Saldo atual do Ativo


## Error
Nome | Tipo | Descrição
---- | ---- | ---------
code | string | Código que identifica a causa do problema
msg | string | Mensagem traduzida
sub_errors | [[]SubError](#suberror) | Lista de sub-erros. Cada sub-erro possui informações mais detalhadas sobre a causa do problema. 


## SubError
Nome | Tipo | Descrição
---- | ---- | ---------
code | string | Código que identifica a causa do problema
msg | string | Mensagem traduzida
field | string | Campo com qual o erro se relaciona. (Somente no caso de [erro de validação](#erro-de-validacao))
