# Entities

## SuccessResponse\<T\>

```javascript
interface SuccessResponse<T> {
  data?: T
  pagination?: Pagination
  error: Error
}
```

Field | Type | Description
---- | ---- | ---------
data | T (optional) | Response information
pagination | [Pagination](#pagination) (optional) | Pagination information
error | [Error](#error) |

## Pagination

```javascript
interface Pagination {
  cursor: string
}
```

Field | Type | Description
---- | ---- | ---------
cursor | Value that can be used to look up the next page

## Data\<T\>

```javascript
interface Data<T> {
  receipt: Receipt
  value: T
}
```

Field | Type | Description
---- | ---- | ---------
receipt | [Receipt](#receipt) | [Receipt of the Action](#actions)
value | T | Generic type that depends on context

## Receipt

```javascript
interface Receipt {
  id: string
  created_at: string
  type: ActionType
}
```

Field | Type | Description
----- | ---- | ---------
id | string | Receipt ID
created_at | string | Receipt creation date
type | [ActionType](#actiontype) | Action type


## ActionType

```javascript
swp.actionTypes.Transfer             // "transfer"
swp.actionTypes.CreateAccount        // "create_account"
swp.actionTypes.CreateOrganization   // "create_organization"
swp.actionTypes.IssueAsset           // "issue_asset"
swp.actionTypes.DestroyAccount       // "destroy_account"
```

Constant | Description
--------- | ---------
transfer | Transfer
create_account | Creation of a new Account
destroy_account | Destruction of an Account
create_organization | Creation of an Organization
issue_asset | Issuing of an Asset


## Organization

```javascript
interface Organization extends Account {
  name: string
}
```

Field | Type | Description
---- | ---- | ---------
id | string | Organization ID
name | string | Name
balances | [Balance[]](#balance) | List of balances held by the Organization for all its Assets


## Account

```javascript
interface Account {
  id: string
  balances: Balance[]
  type?: string
}
```

Field | Type | Description
---- | ---- | ---------
id | string | Account ID
balances | [Balance[]](#balance) | List of balances for all Assets supported by the Account
type | string | String with value `CREATE_ACC`, used for identifying the Action type at serialization/deserialization


## NewAccount

```javascript
interface NewAccount {
  tags: String[]
  starting_balances?: StartingBalance[]
  tags?: string[]
  type?: string
}
```

Field | Type | Description
---- | ---- | ---------
tags | string[] | List of tags
starting_balances | [StartingBalance[]](#startingbalance) | List of Assets to be supported by the new Account with its respective starting balances
type | string | String with value `CREATE_ACC`, used for identifying the Action type at serialization/deserialization


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

Field | Type | Description
---- | ---- | ---------
id | string | Asset ID
code | string | Text between 4 and 12 characters, used for representing the Asset
limit | string | Max amount of the Asset to be issued
tags | string[] | List of tags related to the Asset
type | string | String with value `ISSUE_ASSET`, used for identifying the Action type at serialization/deserialization


## NewAsset

```javascript
interface NewAsset {
  code: string
  limit: string
  tags?: string[]
  type?: string
}
```

Field | Type | Description
---- | ---- | ---------
code | string | Text between 4 and 12 characters, used for representing the Asset
limit | string | Max amount of the Asset to be issued
tags | string[] | List of tags to be related to the Asset
type | string | String with value `ISSUE_ASSET`, used for identifying the Action type at serialization/deserialization


## TransferBatch

```javascript
interface TransferBatch {
  id: string
  actions: Transfer[]
  memo?: string
}
```

Field | Type | Description
---- | ---- | ---------
id | string | Transfer ID
actions | [Transfer[]](#transfer) | List of Transfers included in the transaction
memo | string | Transaction memo


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

Field | Type | Description
---- | ---- | ---------
id | string | Transfer ID for queries
from | string | Sender ID
to | string | Receiver ID
amount | string | Value to be transferred
asset | string | Operation Asset ID
action_code | [ActionCode](#actioncode) | Action response code
type | string (optional) | String with value `TRANSFER`, used for identifying the Action type at serialization/deserialization


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

Field | Type | Description
---- | ---- | ---------
from | string | Sender ID
to | string | Receiver ID
amount | string | Value to be transferred
asset | string | Operation Asset ID
type | string | String with value `TRANSFER`, used for identifying the Action type at serialization/deserialization


## ActionCode

```javascript
swp.actionCodes.Ok            // "action_ok"
swp.actionCodes.Success       // "action_success"
swp.actionCodes.Underfunded   // "action_underfunded"
swp.actionCodes.NotProcessed  // "action_not_processed"
```

Constant | Description
--------- | ---------
**op_ok** | Valid operation
**op_success** | Operation fulfilled successfully
**op_underfunded** | Insufficient balance
**op_not_processed** | Invalid operation

## StartingBalance

```javascript
interface StartingBalance {
  balance: string
  asset_id: string
}
```

Field | Type | Description
---- | ---- | ---------
asset_id | string | Asset ID
balance | string | Current Asset balance


## Balance

```javascript
interface Balance {
  balance: string
  asset_code: string
  asset_id: string
}
```

Field | Type | Description
---- | ---- | ---------
asset_code | string | Asset code
asset_id | string | Asset ID
balance | string | Current Asset balance


## NewTags

```javascript
interface NewTags {
  tags: string[]
}
```

Field | Type | Description
---- | ---- | ---------
tags | string[] | Tags array

## Tags

```javascript
interface Tags {
  id: string
  tags: string[]
}
```

Field | Type | Description
---- | ---- | ---------
id | string | ID of the entity that holds tags
tags | string[] | Tags array

## Error

```javascript
interface Error {
  code: string
  msg: string
  sub_errors: SubError[]
}
```

Field | Type | Description
---- | ---- | ---------
code | string | Code that identifies the cause of the problem
msg | string | Translated message
sub_errors | [SubError[]](#suberror) | List of sub-errors. Each sub-error has more detailed information about the cause of the problem


## SubError

```javascript
interface Error {
  code: string
  msg: string
  field: string
  index: number
}
```

Field | Type | Description
---- | ---- | ---------
code | string | Code that identifies the cause of the problem
msg | string | Translated message
field | string | Field related to the error (only in case of [validation_error](#code))
index | number | Field that identifies which operation failed in the Transfer (only in case of [transfer_failed](#code))


## ActionBatch

```javascript
interface ActionBatch {
  id?: string
  actions: Array<NewAccount | NewAsset | NewTransfer>
  memo?: string
}
```

Field | Type | Description
---- | ---- | ---------
id | string | Batch ID for queries
actions | Array<[NewAccount](#newaccount) &#124; [NewAsset](newasset) &#124; [NewTransfer](newtransfer)> | List with Actions to be performed
memo | string | Transaction memo

## ResponseToken

```javascript
interface ResponseToken {
  token: string
}
```

Field | Type | Description
---- | ---- | ---------
token | string | Token value
