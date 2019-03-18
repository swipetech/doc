# Changelog

**v0.8.0**

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

### Bugfixes

* `GET /transfers` used to return status 500 instead of 404 for invalid `id`

### Breaking changes

#### API

* Error `transfer_invalid_op_length` renamed to `transfer_invalid_actions_length`
* Error `op_underfunded` renamed to `transfer_underfunded`

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
