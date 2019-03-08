### 6. Histórico de Pagamentos

```shell
curl -X GET \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  https://api.swipetech.io/payments?from=<fromID>&to=<toID>&asset=<assetID>
```

```go
res, err := swp.GetPaymentHistory(PaymentFilter{
  From: "from id",
  To: "to id",
  AssetID: "asset id",
})

if !err.Exists() {
  for _, paymentReceipt := range res {
    for _, op := range paymentReceipt.Payment.Operations
      fmt.Printf("%s", op.Amount)
    }
  }
}
```

```javascript
swp.getPaymentHistory({
  from: "from id",
  to: "to id",
  asset_id: "asset_id",
})
  .then(res => {
    res.forEach(paymentReceipt => {
      paymentReceipt.payment.operations.forEach(op => {
        console.log(op.amount)
      })
    })
  })
  .catch(err => {
    console.log(err)
  })
```

Busca todos os Pagamentos segundo alguns filtros especificados. 

`GET /payments?from=<fromID>&to=<toID>&asset=<assetID>`

#### Parâmetros de Query

Parâmetro | Descrição
--------- | ---------
from | ID do remetente (Opcional)
to | ID do destinatário (Opcional)
asset | ID do Ativo (Opcional)

#### Retorno
* **API:** [PaymentsResponse](#paymentsresponse)
* **Go:** ([[ ]PaymentReceipt](#paymentreceipt), [Error](#error))
* **Node:** Promise<[PaymentReceipt[ ]](#paymentreceipt)>












## Monitorar Ações em tempo real

É possível monitorar algumas [Ações](#acao) em tempo real. 
Isso é possível através de [Server-sent events](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events).
Por esse motivo, é necessário incluir no request um header `Accept` com o valor `text/event-stream`.


### 1. Monitorar Pagamentos em tempo real

```shell
curl -X GET \
  -H "Accept: text/event-stream" \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  https://api.swipetech.io/payments?from=<fromID>&to=<toID>&asset=<assetID>
```

```go

```

```javascript
const filters = {
  from: "from id",
  to: "to id",
  asset_id: "asset_id",
}

const eventSource = swp.monitorPayments(
  filters,
  paymentReceipt => {
    paymentReceipt.payment.operations.forEach(op => {
      console.log(op.amount)
    })
  }
)

// Em caso de erro, esse callback será chamado
eventSource.onerror = err => console.log(err)

// Para parar de ouvir os eventos, chame o método close
eventSource.close()
```

Escuta Pagamentos em tempo real segundo alguns filtros especificados.

`GET /payments?from=<fromID>&to=<toID>&asset=<assetID>`

#### Parâmetros de Query

Parâmetro | Descrição
--------- | ---------
from | ID do remetente (Opcional)
to | ID do destinatário (Opcional)
asset | ID do Ativo (Opcional)

#### Headers
Nome | Valor
---- | -----
Accept | text/event-stream

#### Retorno
A cada Pagamento, um evento do tipo `payment` será emitido contendo uma instância de [PaymentReceipt](#paymentreceipt)
