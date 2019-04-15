# Changelog

## **v0.8.0**

Follow code changes on the **javascript** tab on the right.

### New features

* Tags
* Pagination on lists for Accounts, Assets and Transfers
* Destroy Account
* Issue Asset
* Reset Organization (only on sandbox environment)
* Revoke a credential pair
* Starting balances parameter when creating Accounts
* Action batches (create Account, issue Asset and make Transfer)
* Memo in Action batches
* Create Account action does not add all the Organization Assets to the newly created automatically

### Bugfixes

* `GET /transfers` used to return status 500 instead of 404 for invalid `id`
* `DELETE /accounts/:id` did not remove all the Assets
* Create Account with specific Assets did not return balance
* Action index returned was wrong
* `POST /actions` could return a incomplete list

### Breaking changes

#### API

* Error `transfer_invalid_op_length` renamed to `transfer_invalid_actions_length`
* Error `op_underfunded` renamed to `transfer_underfunded`
* OpCode `op_ok` renamed to `action_ok`
* OpCode `op_success` renamed to `action_success`
* OpCode `transfer_underfunded` renamed to `action_underfunded`
* OpCode `op_not_processed` renamed to `action_not_processed`
* Request signature changed from SHA384 to SHA256 and now includes the request `METHOD` 

#### SDK for Node.js

  ```typescript
  interface NewTransferBatch {
    actions: NewTransfer[]
    memo?: string
  }

  interface NewTransfer {
    from: string
    to: string
    asset: string
    amount: string
  }
  ```

* `makeTransfer` function renamed to `makeTransfers`
* `makeTransfers` function now receives a `NewTransferBatch`-type object, which has `"actions"` and `"memo"` keys, instead of a `TransferOperations` list

```typescript
interface Response<T> {
  data: T
  error: Error
}

interface DataReceipt<T> {
  value: T
  receipt: Receipt
}
```

* All functions, except `getAllAccounts`, `getAllAssets` and `getAllTransfers`, now return `Promise<Response<DataReceipt<T>>>` em vez de `Promise<Data<T>>`

```typescript
interface ResponseList<T> {
  data: DataReceipt<T>[]
  pagination: PaginationResponse
  error: Error
}

interface PaginationResponse {
  cursor: number
}
```

* The `getAllAccounts`, `getAllAssets` and `getAllTransfers` functions now return `Promise<ResponseList<T>>` instead of `Promise<Data[]>`
