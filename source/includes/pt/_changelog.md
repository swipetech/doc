# Changelog

**v0.8.0**

Acompanhe alterações de código na aba **javascript** à direita.

### Novas funcionalidades

* Tags
* Paginação nas listagens de Contas, Ativos e Transferências
* Destruir Conta
* Emitir Ativo
* Resetar Organização (apenas em ambiente sandbox)
* Revoke de um par de credenciais
* Parâmetro de saldos iniciais ao criar Conta
* Ações em lote (criar Conta, emitir Ativo e realizar Transferência)
* Memo em lotes de Ações

### Bugfixes

* `GET /transfers` retornava status 500 ao invés de 404 para `id` inválido

### Breaking changes

#### API

* Erro `transfer_invalid_op_length` renomeado para `transfer_invalid_actions_length`
* Erro `op_underfunded` renomeado para `transfer_underfunded`

#### SDK em Node.js

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

* Função `makeTransfer` renomeada para `makeTransfers`
* Função `makeTransfers` passa a receber objeto do tipo `NewTransferBatch`, contendo chaves `"actions"` e `"memo"`, ao invés de lista de `TransferOperations`

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

* Todas as funções, exceto `getAllAccounts`, `getAllAssets` e `getAllTransfers`, passam a retornar `Promise<Response<DataReceipt<T>>>` ao invés de `Promise<Data<T>>`

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

* Funções `getAllAccounts`, `getAllAssets` e `getAllTransfers` passam a retornar `Promise<ResponseList<T>>` ao invés de `Promise<Data[]>`
