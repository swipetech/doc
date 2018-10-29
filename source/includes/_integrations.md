# Integração

Existem algumas maneiras diferentes de integrar à nossa solução. Mantemos SDKs oficiais nas seguintes linguagens:

- [Node.js](https://github.com/Swipetech/swp-node-sdk)

Recomendamos utilizar algum dos SDKs, pois eles abstraem boa parte da complexidade de integração (como autenticação e Server-sent Events).
Por outro lado, nossa API é baseada em REST, sendo completamente possível utilizá-la diretamente.

A cada seção desta documentação, é possível visualizar exemplos à direita para cada modo de integração.

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

Escolha entre as abas **javascript** ou **shell** ao lado para visualizar exemplos básicos de utilização dos SDKs ou API Rest.

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

```javascript
swp.getOrganization()
  .then(data => console.log(data.value.name))
  .catch(error => 
    console.log(error)
  )
```

Busca informações sobre sua Organização.

`GET /organizations`

#### Retorno
* **API:** [OrganizationResponse](#organizationresponse)
* **Node:** Promise<[OrganizationReceipt](#organizationreceipt)>


### 2. Todas as Contas

```shell
curl -X GET \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  https://api.swipetech.io/accounts
```

```javascript
swp.getAllAccounts()
  .then(data => {
    data.forEach(accountReceipt => 
      console.log(accountReceipt.value.id)
    )
  })
  .catch(error => 
    console.log(error)
  )
```

Busca informações sobre todas as Contas já criadas pela sua Organização.

`GET /accounts`

#### Retorno
* **API:** [AccountsResponse](#accountsresponse)
* **Node:** Promise<[AccountReceipt[ ]](#accountreceipt)>


### 3. Uma Conta específica

```shell
curl -X GET \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  https://api.swipetech.io/accounts/44d351a02f2307153be74984a59675f2733ad5deb1fa9fb08b0a36fe3d15fd6d
```

```javascript
swp.getAccount("44d351a02f2307153be74984a59675f2733ad5deb1fa9fb08b0a36fe3d15fd6d")
  .then(accountReceipt => {
    console.log(accountReceipt.value.id)
  })
  .catch(error => 
    console.log(error)
  )
```

Busca informações sobre uma Conta específica criada pela sua Organização.

`GET /accounts/:id`

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
id | ID da Conta 

#### Retorno
* **API:** [AccountResponse](#accountresponse)
* **Node:** Promise<[AccountReceipt](#accountreceipt)>


### 4. Todos os Ativos

```shell
curl -X GET \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  https://api.swipetech.io/assets
```

```javascript
swp.getAllAssets()
  .then(data => {
    data.forEach(assetReceipt => 
      console.log(assetReceipt.value.code)
    )
  })
  .catch(error => 
    console.log(error)
  )
```

Busca todos os Ativos criados pela sua Organização.

`GET /assets`

#### Retorno
* **API:** [AssetsResponse](#assetsresponse)
* **Node:** Promise<[AssetReceipt](#assetreceipt)>

### 5. Informações sobre um Pagamento específico

```shell
curl -X GET \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  https://api.swipetech.io/payments/44d351a02f2307153be74984a59675f2733ad5deb1fa9fb08b0a36fe3d15fd6d
```

```javascript
swp.getPayment("44d351a02f2307153be74984a59675f2733ad5deb1fa9fb08b0a36fe3d15fd6d")
  .then(data => {
    data.value.operations.forEach(op => {
      console.log(op.amount)
    })
  })
  .catch(error => 
    console.log(error)
  )
```

Busca informações sobre um Pagamento relacionado à sua Organização ou Contas filhas. 

`GET /payments/:id`

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
id | ID do Pagamento 

#### Retorno
* **API:** [PaymentResponse](#paymentresponse)
* **Node:** Promise<[PaymentReceipt](#paymentreceipt)> 


## Executar ações

```shell
curl -X POST \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  https://api.swipetech.io/accounts
```

```javascript
swp.createAccount()
  .then(accountReceipt => 
    console.log(accountReceipt.value.id)
  )
  .catch(error => 
    console.log(error)
  )
```

### 1. Criar nova Conta

`POST /accounts`

#### Retorno
* **API:** [AccountResponse](#accountresponse)
* **Node:** Promise<[AccountReceipt](#accountreceipt)>


### 2. Emitir um Ativo

<aside class="notice">No momento esta funcionalidade ainda não está aberta. Entre em contato conosco para mais detalhes.</aside>


### 3. Realizar um Pagamento

```shell
curl --request POST \
  -L https://api.sandbox.swipetech.io/payments \
  -H 'accept: application/json' \
  -H 'accept-language: pt-BR' \
  -H 'content-type: application/json' \
  -H 'x-swp-api-key: <sua chave de api>' \
  -H 'x-swp-signature: <assinatura da requisição>' \
  -d '{"operations":[{"from":"269de13d714b253b88fdf18620c3194078f7932d48855efc6e4d6dc57528c84c","to":"b0ea341bd255aa27eb38ef136aebfcaaffbc87103d872a4a218df7b434f5a6ad","amount":121.22,"asset":"b6039b3fb9c3e30945644cc394e6b1accb0a6c2844514aad0819a89d64b0184c"}]}'
```

```javascript
// Perceba que é uma lista, mesmo que seja somente uma Operação no Pagamento
swp.makePayment([
  {
    from: "44d351a02f2307153be74984a59675f2733ad5deb1fa9fb08b0a36fe3d15fd6d",
    to: "55c86a9027f2ff8c5d6ed1e2dbda01886b8b33f461341533d7391c14abe7aa40",
    asset: "07773f06becd47385d1e8d1e9bad3bd588ccd880fe746819257a6246e33551d3",
    amount: 1000,
  }
])
  .then(data => {
    data.value.operations.forEach(op => {
      console.log(op.amount)
    })
  })
  .catch(error => {
    console.log(error)
    
    // exibir índice e código de erro dos pagamentos que falharam
    error.sub_errors
      .forEach((se, i) => {
        // é possível checar qual a operação que falhou através de seu campo `index` 
        console.log(i, se.code, se.index)
      })
  })
```

`POST /payments`

#### Body
[Payment](#payment)

#### Retorno
* **API:** [PaymentResponse](#paymentresponse)
* **Node:** Promise<[PaymentReceipt](#paymentreceipt)>
