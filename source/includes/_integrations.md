# Integração

Existem algumas maneiras diferentes de integrar à nossa solução. Mantemos SDKs oficiais nas seguintes linguagens:

- [Golang](https://github.com/Swipetech/swp-go-sdk)
- [Node.js](https://github.com/Swipetech/swp-node-sdk)

Além disso, nossa API é baseada em REST, por isso é completamente possível utilizá-la diretamente. 

A cada seção desta documentação, é possível visualizar exemplos à direita para cada modo de integração.

<aside class="notice">Temos um limite de 5 requests/segundo por IP (Caso necessite exceder esse limite, entre em contato conosco). Se ultrapassar isso, você receberá um erro com código <code>too_many_requests</code>.</aside>

## REST API

Selecione a aba **shell** ao lado para visualizar exemplos básicos de integração via API utilizando `curl`.

<aside class="warning">Lembre-se de incluir <code>https</code> em todas suas requisições. Em caso de uma conexão não segura, a API retornará um erro com código <code>insecure_connection</code>.</aside>

### Ambiente de Produção

Para utilizar o ambiente de Produção, utilize o seguinte domínio para suas requisições:

`https://api.swipetech.io`

### Ambiente de Sandbox

Para utilizar o ambiente de Sandbox, utilize o seguinte domínio para suas requisições:
 
`https://api.sandbox.swipetech.io`

### Autenticação

Só é necessário se preocupar com a autenticação das chamadas caso opte por integrar diretamente à nossa API. 
Nossos SDKs abstraem completamente essa complexidade.

#### Headers

```shell
curl -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <API_KEY>" \
  -H "X-Swp-Signature: <SIGNATURE>" \
  https://api.swipetech.io/organizations/
```

Utilizamos um modelo de `Api Key` e `Secret` para autenticar as requisições.
Todas devem incluir ao menos dois headers:

- `X-Swp-Api-Key`: Api Key como string
- `X-Swp-Signature`: Assinatura do request (Como explicado logo abaixo)

#### Assinatura (X-Swp-Signature)

```go
requestPath := "/accounts"
bodyString := ""
secret := "71ad81f98fbbab22c9d74948d2899a65027208197291d11e2065c3a9c62fe1f0"

stringToSign := []byte(fmt.Sprintf("%s%s", requestPath, bodyString))

hash := hmac.New(sha512.New384, []byte(yourSecret))
hash.Write(stringToSign)

signature := base64.StdEncoding.EncodeToString(hash.Sum(nil))

fmt.Println(signature)
//prmoNrKxBwif+GSp8b1hJ3mQ9sjU3NrJNmlKLyNkR2HelXM+CtB6+PyeZk/cQv6a
```

```javascript
const Crypto = require("crypto-js")
const Base64 = require("crypto-js/enc-base64")
const requestPath = "/accounts"
const bodyString = ""
const secret = "71ad81f98fbbab22c9d74948d2899a65027208197291d11e2065c3a9c62fe1f0"

const stringToSign = requestPath + bodyString
const hmac = Crypto.HmacSHA384(stringToSign, secret)
const signature = Base64.stringify(hmac)

console.log(signature)
//prmoNrKxBwif+GSp8b1hJ3mQ9sjU3NrJNmlKLyNkR2HelXM+CtB6+PyeZk/cQv6a
```

Para gerar uma assinatura do request, siga esses passos:

- Concatene o `path` completo do request com a string do `body` (JSON).
- Crie um HMAC-SHA-384 utilizando sua `secret` na string obtida acima.
- Finalmente, converta o resultado para Base64

Exemplos de assinatura:

Campo | Valor 
----  | -----
**Path** | /accounts
**Body** | 
**Secret** | 71ad81f98fbbab22c9d74948d2899a65027208197291d11e2065c3a9c62fe1f0
**Assinatura** | prmoNrKxBwif+GSp8b1hJ3mQ9sjU3NrJNmlKLyNkR2HelXM+CtB6+PyeZk/cQv6a

Campo | Valor 
----  | -----
**Path** | /payments
**Body** | {"source_id":"8917a52eb52f652d247e0a41f0986656bcac926ddf55c4967eda264111b407f8"}
**Secret** | 71ad81f98fbbab22c9d74948d2899a65027208197291d11e2065c3a9c62fe1f0
**Assinatura** | e/rQsFoKvz7BhQWngFiEZCrXhXOHzhuy60jRpRQeeP87snLNYndnTw+vzaH9blqD

### Idioma

```shell
curl -H "Content-Type: application/json" \
  -H "Accept-Language: en-US" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  https://api.swipetech.io/organizations/
```

Para configurar o idioma de resposta da API, utilize o seguinte header nas requisições:

- `Accept-Language`: [Idioma desejado](#idiomas-suportadas) (`pt-BR` por padrão)

## SDKs

Escolha entre as abas **go** ou **javascript** ao lado para visualizar exemplos básicos de utilização dos SDKs.

### Instalação Golang

Para baixar a versão mais recente:

`go get github.com/swipetech/swp-go-sdk`

Para baixar uma versão específica

`go get github.com/swipetech/swp-go-sdk@<version>`

<aside class="notice">Lembre-se de substituir <code>&lt;version&gt;</code> pela versão desejada (e.g. <code>0.5.0</code>)</aside>

### Instalação Node.js

```javascript
// ES2015 ou TypeScript
import * as swipe from '@swp/swipe-sdk'
```

```javascript
// CommonJS
const swipe = require('@swp/swipe-sdk')
```

Via npm:

`npm i @swp/swipe-sdk`

Via yarn:

`yarn add @swp/swipe-sdk`

<aside class="warning"><b>Atenção:</b> a integração deve ser realizada sempre a partir de um servidor Node.js, nunca a partir de um navegador. Inicializar o SDK JavaScript a partir de um navegador poderá expor indevidamente seu <b>Api Key</b> e <b>Secret</b>, mesmo que sejam utilizadas práticas de <i>code obfuscation</i>.</aside>

### Inicialização

```go
// Inicia no ambiente de Produção, utilizando 'pt-BR'
swp := swipe.Init(
  "your api key",
  "your secret key",
  wallet.PT_BR,
)
```

```go
// Inicia no ambiente de Sandbox, utilizando 'en-US'
swp := swipe.InitSandbox(
  "your api key",
  "your secret key",
  wallet.EN_US,
)
```

```javascript
// Inicia no ambiente de Produção, utilizando 'pt-BR'
const swp = Swipe.init({
  apiKey: "your api key",
  secret: "your secret key",
})
```

```javascript
// Inicia no ambiente de Sandbox, utilizando 'en-US'
const swp = Swipe.init({
  apiKey: "your api key",
  secret: "your secret key",
  sandbox: true,
  language: Wallet.languages.EN_US,
})
```

O primeiro passo, após a [instalação](#sdks), é inicializar o SDK com uma `Api Key`, um `Secret` e um `Idioma` válidos.

Para fins de testes, recomendamos que utilize nosso ambiente de Sandbox.


## Buscar informações

Utilize os endpoints abaixo para buscar mais detalhes sobre sua [Organização](#organizacao), suas [Contas](#conta) ou [Ativos](#ativo).

### 1. Organização

```shell
curl -X GET \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  https://api.swipetech.io/organizations
```

```go
res, err := swp.GetOrganization()

if !err.Exists() {
  fmt.Println(res.Organization.Name)
}
```

```javascript
swp.getOrganization()
  .then(res => {
    console.log(res.organization.name)
  })
  .catch(err => {
    console.log(err)
  })
```

Busca informações sobre sua Organização.

`GET /organizations`

#### Retorno
* **API:** [OrganizationResponse](#organizationresponse)
* **Go:** ([OrganizationReceipt](#organizationreceipt), [Error](#error))
* **Node:** Promise<[OrganizationReceipt](#organizationreceipt)>


### 2. Todas as Contas

```shell
curl -X GET \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  https://api.swipetech.io/accounts
```

```go
res, err := swp.GetAllAccounts()

if !err.Exists() {
  for _, accReceipt := range res {
    fmt.Println(accReceipt.Account.ID)
  }
}
```

```javascript
swp.getAllAccounts()
  .then(res => {
    res.forEach(accReceipt => {
      console.log(accReceipt.account.id)
    })
  })
  .catch(err => {
    console.log(err)
  })
```

Busca informações sobre todas as Contas já criadas pela sua Organização.

`GET /accounts`

#### Retorno
* **API:** [AccountsResponse](#accountsresponse)
* **Go:** ([[ ]AccountReceipt](#accountreceipt), [Error](#error))
* **Node:** Promise<[AccountReceipt[ ]](#accountreceipt)>


### 3. Uma Conta específica

```shell
curl -X GET \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  https://api.swipetech.io/accounts/<id_da_conta>
```

```go
res, err := swp.GetAccount("id da conta")

if !err.Exists() {
  fmt.Println(res.Account.ID)
}
```

```javascript
swp.getAccount("id da conta")
  .then(res => {
    console.log(res.account.id)
  })
  .catch(err => {
    console.log(err)
  })
```

Busca informações sobre uma Conta específica criada pela sua Organização.

`GET /accounts/:id`

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
id | ID da Conta 

#### Retorno
* **API:** [AccountResponse](#accountresponse)
* **Go:** ([AccountReceipt](#accountreceipt), [Error](#error))
* **Node:** Promise<[AccountReceipt](#accountreceipt)>


### 4. Todos os Ativos

```shell
curl -X GET \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  https://api.swipetech.io/assets
```

```go
res, err := swp.GetAllAssets()

if !err.Exists() {
  for _, assetReceipt := range res {
    fmt.Println(assetReceipt.Asset.Code)
  }
}
```

```javascript
swp.getAllAssets()
  .then(res => {
    res.forEach(assetReceipt => {
      console.log(assetReceipt.asset.code)
    })
  })
  .catch(err => {
    console.log(err)
  })
```

Busca todos os Ativos criados pela sua Organização.

`GET /assets`

#### Retorno
* **API:** [AssetsResponse](#assetsresponse)
* **Go:** ([[ ]AssetReceipt](#assetreceipt), [Error](#error))
* **Node:** Promise<[AssetReceipt [ ]](#assetreceipt)>

### 5. Informações sobre um Pagamento específico

```shell
curl -X GET \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  https://api.swipetech.io/payments/<id_do_pagamento>
```

```go
res, err := swp.GetPayment("id do pagamento")

if !err.Exists() {
  for _, op := range res.Payment.Operations {
    fmt.Printf("%s", op.Amount)
  }
}
```

```javascript
swp.getPayment("id do pagamento")
  .then(res => {
    res.payment.operations.forEach(op => {
      console.log(op.amount)
    })
  })
  .catch(err => {
    console.log(err)
  })
```

Busca informações sobre um Pagamento relacionado à sua Organização ou Contas filhas. 

`GET /payments/:id`

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
id | ID do Pagamento 

#### Retorno
* **API:** [PaymentResponse](#paymentresponse)
* **Go:** ([PaymentReceipt](#paymentreceipt), [Error](#error))
* **Node:** Promise<[PaymentReceipt](#paymentreceipt)> 


## Executar ações

### 1. Criar nova Conta

`POST /accounts`

#### Retorno
[AccountResponse](#accountresponse)


### 2. Emitir um Ativo

<aside class="notice">No momento esta funcionalidade ainda não está aberta. Entre em contato conosco para mais detalhes.</aside>


### 3. Realizar um Pagamento

`POST /payments`

#### Body
[[]PaymentOperations](#paymentoperation)

#### Retorno
[PaymentsResponse](#paymentsresponse)
