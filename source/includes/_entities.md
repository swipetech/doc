# Estruturas

## OrganizationResponse

```go
type OrganizationResponse struct {
	Data  OrganizationReceipt
	Error swperrors.Error
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
data | [OrganizationReceipt](#organizationreceipt) | Informações de uma Organização com um recibo de sua criação 
error | [Error](#error) |


## AccountResponse

```go
type AccountResponse struct {
	Data  AccountReceipt
	Error swperrors.Error
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
data | [AccountReceipt](#accountreceipt) | Informações de uma Conta com um recibo de sua criação
error | [Error](#error)


## AccountsResponse

```go
type AccountsResponse struct {
	Data []AccountReceipt
	Error swperrors.Error
}
```
Campo | Tipo | Descrição
---- | ---- | ---------
data | [[]AccountReceipt](#accountreceipt) | Lista de informações de Conta com um recibo de sua criação
error | [Error](#error) 


## AssetsResponse

```go
type AssetsResponse struct {
	Data  []AssetReceipt
	Error swperrors.Error
}
```
Campo | Tipo | Descrição
---- | ---- | ---------
data | [[]AssetReceipt](#assetreceipt) | Informações de um Ativo com um recibo de sua criação
error | [Error](#error) | 


<!-- ## PaymentsResponse
Campo | Tipo | Descrição
---- | ---- | ---------
data | [[]PaymentReceipt](#paymentreceipt) | Lista de informações sobre um Pagamento com um recibo de cada ocorrência
error | [Error](#error) |  -->


## PaymentResponse

```go
type PaymentResponse struct {
	Data  PaymentReceipt
	Error swperrors.Error
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
data | [PaymentReceipt](#paymentreceipt) | Informações sobre um Pagamento com um recibo de sua ocorrência
error | [Error](#error) | 


## OrganizationReceipt

```go
type OrganizationReceipt struct {
	Receipt      Receipt
	Organization Organization
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
receipt | [Receipt](#receipt) | Recibo da criação da Organização
organization | [Organization](#organization) | Informações do Organização


## AccountReceipt

```go
type AccountReceipt struct {
	Receipt Receipt
	Account Account
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
receipt | [Receipt](#receipt) | Recibo da criação da Conta
account | [Account](#account) | Informações do Conta


## AssetReceipt

```go
type AssetReceipt struct {
	Receipt Receipt
	Asset   Asset
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
receipt | [Receipt](#receipt) | Recibo da criação do Ativo
asset | [Asset](#asset) | Informações do Ativo


## PaymentReceipt

```go
type PaymentReceipt struct {
	Receipt Receipt
	Payment Payment
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
receipt | [Receipt](#receipt) | Recibo da ocorrência do Pagamento
payment | [Payment](#payment) | Informações do Pagamento 


## Receipt

```go
type Receipt struct {
	ID        string
	CreatedAt time.Time
	OpType    swptx.OpType
}
```

Campo | Tipo | Descrição
----- | ---- | ---------
id | string | ID do Recibo
created_at | string | Data de criação do Recibo
op_type | OpType | Tipo do recibo


## OpType

```go
swptx.Payment             // "payment"
swptx.CreateAccount       // "create_account"
swptx.CreateOrganization  //"create_organization"
swptx.IssueAsset          // "issue_asset"
swptx.ChangeTrust         // "change_trust"
```

Constante | Descrição
--------- | ---------
payment | Pagamento
create_account | Criação de Conta
create_organization | Criação de Organização
issue_asset | Emissão de Ativo


## Organization

```go
type Organization struct {
	Account
	Name   string
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
id | string | ID da Organização
name | string | Nome
balances | [[]Balance](#balance) | Lista de saldos da Organização para todos seus Ativos


## Account

```go
type Account struct {
	ID        string
	Balances  []Balance
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
id | string | ID da Conta
balances | [[]Balance](#balance) | Lista de saldos para todos os Ativos suportados pela Conta


## Asset

```go
type Asset struct {
	ID    string
	Code  string
	Limit float64
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
id | string | ID do Ativo
code | string | Texto entre 4 e 12 caracteres que representa o Ativo
limit | float64 | Número máximo de unidades a ser emitido
 
 
## Payment

```go
type Payment struct {
	ID         string
	Operations []PaymentOp
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
id | string | ID do Pagamento
operations | [[]PaymentOperation](#paymentoperation) | Lista de operações incluídas no Pagamento


## PaymentOperation

```go
type PaymentOperation struct {
	From   string
	To     string
	Amount float64
	Asset  string
	OpCode swptx.OpCode
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
from | string | ID do remetente
to | string | ID do destinatário
amount | float64 | Valor a ser transferido
asset | string | ID do Ativo do Pagamento
op_code | OpCode | Código de resposta da Operação


## OpCode

```go
swptx.Ok           // "op_ok"
swptx.Success      // "op_success"
swptx.Underfunded  // "op_underfunded"
swptx.NotProcessed // "op_not_processed"
```

Constante | Descrição
--------- | ---------
**op_ok** | Operação válida
**ok_success** | Operação executada com sucesso
**op_underfunded** | Saldo insuficiente
**op_not_processed** | Operação inválida


## Balance

```go
type Balance struct {
	Balance   float64
	AssetCode string
	AssetID   string
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
asset_code | string | código do Ativo
asset_id | string | ID do Ativo
balance | float64 | Saldo atual do Ativo


## Error

```go
type Error struct {
	StackError    error
	OriginalError error
	Code          ErrorCode
	Msg           string
	SubErrors     []SubError
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
code | string | Código que identifica a causa do problema
msg | string | Mensagem traduzida
sub_errors | [[]SubError](#suberror) | Lista de sub-erros. Cada sub-erro possui informações mais detalhadas sobre a causa do problema. 


## SubError

```go
type SubError struct {
	Code  string
	Msg   string
	Field string
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
code | string | Código que identifica a causa do problema
msg | string | Mensagem traduzida
field | string | Campo com qual o erro se relaciona. (Somente no caso de [erro de validação](#erro-de-validacao))
