# O que há de novo na versão 0.8.0?

## Novas funcionalidades

* Paginação nas listagens de Contas, Ativos e Transferências;
* Tags em Contas e Ativos;
* Memo em lote de Transferências ou Ações;
* Resetar Organização (apenas em ambiente sandbox);
* Excluir Conta;
* Revogar Credenciais da Organização;
* Saldo inicial ao criar Conta;
* Ações em lote (criar Conta, emitir Ativo e realizar Transferência);
* Emissão de Ativos;


## Bugfixes

* `GET /transfers` retornava status 500 ao invés de 404 para `id` inválido;


## Breaking changes

### API

* Erros renomeados:
    * `transfer_invalid_op_length` -> `transfer_invalid_actions_length`
    * `op_underfunded` -> `transfer_underfunded`


### SDK Node.js

* Função `makeTransfer` renomeada para `makeTransfers`;
* Função `makeTransfers` passa a receber objeto do tipo `NewTransferBatch`, contendo chaves `"actions"` e `"memo"`, ao invés de lista de `TransferOperations`;
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
* Todas as funções, exceto `getAllAccounts`, `getAllAssets` e `getAllTransfers`, passam a retornar `Promise<Response<DataReceipt<T>>>` ao invés de `Promise<Data<T>>`:
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

* Funções `getAllAccounts`, `getAllAssets` e `getAllTransfers` passam a retornar `Primise<ResponseList<T>>` ao invés de `Promise<Data[]>`:
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
