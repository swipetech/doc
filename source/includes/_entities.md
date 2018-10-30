# Estruturas

## OrganizationResponse

```javascript
interface OrganizationResponse {
  data: Data<Organization>
  error: Error
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
data | [Data\<Organization\>](#data-lt-t-gt) | Informações de uma Organização com um recibo de sua criação 
error | [Error](#error) |


## AccountResponse

```javascript
interface AccountResponse {
  data: Data<Account>
  error: Error
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
data | [Data\<Account\>](#data-lt-t-gt) | Informações de uma Conta com um recibo de sua criação
error | [Error](#error)


## AccountsResponse

```javascript
interface AccountsResponse {
  data: Data<Account>[]
  error: Error
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
data | [Data\<Account\>[]](#data-lt-t-gt) | Lista de informações de Contas com um recibo de sua criação
error | [Error](#error) 


## AssetsResponse

```javascript
interface AssetsResponse {
  data: Data<Asset>[]
  error: Error
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
data | [Data\<Asset\>[]](#data-lt-t-gt) | Lista de informações de Ativos com um recibo de sua criação
error | [Error](#error) | 


## TransferResponse

```javascript
interface TransferResponse {
  data: Data<Transfer>
  error: Error
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
data | [Data\<Transfer\>](#data-lt-t-gt) | Informações sobre uma Transferência com um recibo de sua confirmação
error | [Error](#error) | 


## Data\<T\>

```javascript
interface Data<T> {
  receipt: Receipt
  value: T
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
receipt | [Receipt](#receipt) | [Recibo](#acao) da Ação
value | T | Tipo genérico que depende do contexto

## Receipt

```javascript
interface Receipt {
  id: string
  created_at: string
  op_type: OperationType
}
```

Campo | Tipo | Descrição
----- | ---- | ---------
id | string | ID do Recibo
created_at | string | Data de criação do Recibo
op_type | [OpType](#optype) | Tipo do recibo


## OpType

```javascript
swp.operationTypes.Transfer              // "transfer"
swp.operationTypes.CreateAccount        // "create_account"
swp.operationTypes.CreateOrganization   //"create_organization"
swp.operationTypes.IssueAsset           // "issue_asset"
```

Constante | Descrição
--------- | ---------
transfer | Transferência
create_account | Criação de Conta
create_organization | Criação de Organização
issue_asset | Emissão de Ativo


## Organization

```javascript
interface Organization extends Account {
  name: string
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
id | string | ID da Organização
name | string | Nome
balances | [Balance[]](#balance) | Lista de saldos da Organização para todos seus Ativos


## Account

```javascript
interface Account {
  id: string
  balances: Balance[]
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
id | string | ID da Conta
balances | [Balance[]](#balance) | Lista de saldos para todos os Ativos suportados pela Conta


## Asset

```javascript
interface Asset {
  id: string
  code: string
  limit: number
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
id | string | ID do Ativo
code | string | Texto entre 4 e 12 caracteres que representa o Ativo
limit | float64 | Número máximo de unidades a ser emitido
 
 
## Transfer

```javascript
interface Transfer {
  id: string
  operations: TransferOperation[]
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
id | string | ID da Transferência
operations | [TransferOperation[]](#transferoperation) | Lista de operações incluídas na Transferência


## TransferOperation

```javascript
interface TransferOperation {
  from: string
  to: string
  asset: string
  amount: number
  op_code: OperationCode
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
from | string | ID do remetente
to | string | ID do destinatário
amount | float64 | Valor a ser transferido
asset | string | ID do Ativo da Transferência
op_code | [OpCode](#opcode) | Código de resposta da Operação


## OpCode

```javascript
swp.operationCodes.Ok            // "op_ok"
swp.operationCodes.Success       // "op_success"
swp.operationCodes.Underfunded   // "op_underfunded"
swp.operationCodes.NotProcessed  // "op_not_processed"
```

Constante | Descrição
--------- | ---------
**op_ok** | Operação válida
**op_success** | Operação executada com sucesso
**op_underfunded** | Saldo insuficiente
**op_not_processed** | Operação inválida


## Balance

```javascript
interface Balance {
  balance: number
  asset_code: string
  asset_id: string
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
asset_code | string | código do Ativo
asset_id | string | ID do Ativo
balance | float64 | Saldo atual do Ativo


## Error

```javascript
interface Error {
  code: string
  msg: string
  sub_errors: SubError[]
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
code | string | Código que identifica a causa do problema
msg | string | Mensagem traduzida
sub_errors | [SubError[]](#suberror) | Lista de sub-erros. Cada sub-erro possui informações mais detalhadas sobre a causa do problema. 


## SubError

```javascript
interface Error {
  code: string
  msg: string
  field: string
  index: number
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
code | string | Código que identifica a causa do problema
msg | string | Mensagem traduzida
field | string | Campo com qual o erro se relaciona. (Somente no caso de [validation_error](#code))
index | number | Campo para identificar qual operação dentro da transferência falhou. (Somente no caso de [transfer_failed](#code))
