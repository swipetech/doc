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

* `GET /transfers` retornava status 500 em vez de 404 para `id` inválido

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
* Função `makeTransfers` passa a receber objeto do tipo `NewTransferBatch`, contendo chaves `"actions"` e `"memo"`, em vez de lista de `TransferOperations`

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

* Todas as funções, exceto `getAllAccounts`, `getAllAssets` e `getAllTransfers`, passam a retornar `Promise<Response<DataReceipt<T>>>` em vez de `Promise<Data<T>>`

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

* Funções `getAllAccounts`, `getAllAssets` e `getAllTransfers` passam a retornar `Promise<ResponseList<T>>` em vez de `Promise<Data[]>`

# Changelog

**v0.8.1**

Acompanhe alterações de código na aba **javascript** à direita.

### Bugfixes

* `DELETE /accounts/:id` não estava removendo os assets
* Conta não estava permitindo receber e enviar asset em uma mesma batch
* Criar contas com um saldo mínimo não estava retornando o saldo
* Invés do índice da ação da aplicação o sistema estava retornando o índice da operação do stellar
* `POST /actions` poderia retorna uma lista de ações incompleta

#### API

* OpCode `op_ok` renomeado para `action_ok`
* OpCode `op_success` renomeado para `action_success`
* OpCode `transfer_underfunded` renomeado para `action_underfunded`
* OpCode `op_not_processed` renomeado para `action_not_processed`