# Estruturas

## SuccessResponse\<T\>

```javascript
interface SuccessResponse<T> {
  data?: T
  pagination?: Pagination
  error: Error
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
data | T (opcional) | Informações da resposta
pagination | [Pagination](#pagination) (opcional) | Informações sobre a paginação
error | [Error](#error) |

## Pagination

```javascript
interface Pagination {
  cursor: string
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
cursor | Valor que pode ser utilizado para consultar a próxima página

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
  type: ActionType
}
```

Campo | Tipo | Descrição
----- | ---- | ---------
id | string | ID do Recibo
created_at | string | Data de criação do Recibo
type | [ActionType](#actiontype) | Tipo da Ação


## ActionType

```javascript
swp.actionTypes.Transfer             // "transfer"
swp.actionTypes.CreateAccount        // "create_account"
swp.actionTypes.CreateOrganization   // "create_organization"
swp.actionTypes.IssueAsset           // "issue_asset"
swp.actionTypes.DestroyAccount       // "destroy_account"
```

Constante | Descrição
--------- | ---------
transfer | Transferência
create_account | Criação de uma nova Conta
destroy_account | Destruição de uma Conta
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
  type?: string
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
id | string | ID da Conta
balances | [Balance[]](#balance) | Lista de saldos para todos os Ativos suportados pela Conta
type | string | String com valor `CREATE_ACC`, utilizada para identificar o tipo de Action na serialização/deserialização


## NewAccount

```javascript
interface NewAccount {
  tags: String[]
  starting_balances?: StartingBalance[]
  tags?: string[]
  type?: string
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
tags | string[] | Lista de Tags
starting_balances | [StartingBalance[]](#startingbalance) | Lista de Ativos que a nova Conta suportará e seus respectivos saldos iniciais
type | string | String com valor `CREATE_ACC`, utilizada para identificar o tipo de Action na serialização/deserialização


## Asset

```javascript
interface Asset {
  id: string
  code: string
  limit: string
  tags: string[]
  type?: string
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
id | string | ID do Ativo
code | string | Texto entre 4 e 12 caracteres que representa o Ativo
limit | string | Número máximo de unidades a ser emitido
tags | string[] | Lista de tags associadas ao Ativo
type | string | String com valor `ISSUE_ASSET`, utilizada para identificar o tipo de Action na serialização/deserialização


## NewAsset

```javascript
interface NewAsset {
  code: string
  limit: string
  tags?: string[]
  type?: string
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
code | string | Texto entre 4 e 12 caracteres que representa o Ativo
limit | string | Número máximo de unidades a ser emitido
tags | string[] | Lista de tags a serem associadas ao Ativo
type | string | String com valor `ISSUE_ASSET`, utilizada para identificar o tipo de Action na serialização/deserialização


## TransferBatch

```javascript
interface TransferBatch {
  id: string
  actions: Transfer[]
  memo?: string
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
id | string | ID da Transferência
actions | [Transfer[]](#transfer) | Lista de Transferências incluídas na transação
memo | string | Memo da transação


## Transfer

```javascript
interface Transfer {
  id: string
  from: string
  to: string
  asset: string
  amount: string
  op_code: OperationCode
  type?: string
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
id | string | ID da transferência para consultas
from | string | ID do remetente
to | string | ID do destinatário
amount | string | Valor a ser transferido
asset | string | ID do Ativo da Operação
op_code | [OpCode](#opcode) | Código de resposta da Operação
type | string (opcional) | String com valor `TRANSFER`, utilizada para identificar o tipo de Action na serialização/deserialização


## NewTransfer

```javascript
interface NewTransfer {
  from: string
  to: string
  amount: string
  asset: string
  type?: string
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
from | string | ID do remetente
to | string | ID do destinatário
amount | string | Valor a ser transferido
asset | string | ID do Ativo da Operação
type | string | String com valor `TRANSFER`, utilizada para identificar o tipo de Action na serialização/deserialização


## OpCode

```javascript
swp.operationCodes.Ok            // "transfer_ok"
swp.operationCodes.Success       // "transfer_success"
swp.operationCodes.Underfunded   // "transfer_underfunded"
swp.operationCodes.NotProcessed  // "transfer_not_processed"
```

Constante | Descrição
--------- | ---------
**transfer_ok** | Transferência válida
**transfer_success** | Transferência executada com sucesso
**transfer_underfunded** | Saldo insuficiente
**transfer_not_processed** | Transferência inválida

## StartingBalance

```javascript
interface StartingBalance {
  balance: string
  asset_id: string
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
asset_id | string | ID do Ativo
balance | string | Saldo atual do Ativo


## Balance

```javascript
interface Balance {
  balance: string
  asset_code: string
  asset_id: string
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
asset_code | string | código do Ativo
asset_id | string | ID do Ativo
balance | string | Saldo atual do Ativo


## NewTags

```javascript
interface NewTags {
  tags: string[]
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
tags | string[] | Array de tags

## Tags

```javascript
interface Balance {
  id: string
  tags: string[]
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
id | string | Id da entidade que possui as tags
tags | string[] | Array de tags

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


## ActionBatch

```javascript
interface ActionBatch {
  id?: string
  actions: Array<NewAccount | NewAsset | NewTransfer>
  memo?: string
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
id | string | ID do lote para consultas
actions | Array<[NewAccount](#newaccount) &#124; [NewAsset](newasset) &#124; [NewTransfer](newtransfer)> | Lista com Ações a serem executadas
memo | string | Memo da transação

## ResponseToken

```javascript
interface ResponseToken {
  token: string
}
```

Campo | Tipo | Descrição
---- | ---- | ---------
token | string | Valor do token
