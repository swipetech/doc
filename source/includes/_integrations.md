# Integração

Existem diferentes maneiras de integrar à nossa solução.

Mantemos um SDK oficial em [Node.js](https://github.com/Swipetech/swp-node-sdk). Recomendamos a utilização do SDK, pois ele abstrai boa parte da complexidade de integração (como autenticação).
Por outro lado, nossa API é baseada em REST, sendo completamente possível utilizá-la diretamente.

A cada seção desta documentação, é possível visualizar exemplos à direita para integração via SDK ou REST.

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
Nosso SDK abstrai completamente essa complexidade.

#### Headers

```shell
curl -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <API_KEY>" \
  -H "X-Swp-Signature: <SIGNATURE>" \
  https://api.swipetech.io/organizations/
```

Utilizamos um modelo de `Api Key` e `Secret` para autenticar as requisições.
Todas devem incluir os seguintes headers:

- `X-Swp-Timestamp`: Número de segundos desde [Unix Epoch](https://en.wikipedia.org/wiki/Unix_time). 
Aceita-se uma margem de 5 minutos de diferença com o horário do recebimento da requisição pela API.
- `X-Swp-Api-Key`: Api Key como string
- `X-Swp-Signature`: Assinatura do request (Como explicado logo abaixo)

#### Assinatura (X-Swp-Signature)

```javascript
const Crypto = require("crypto-js")
const Base64 = require("crypto-js/enc-base64")
const requestPath = "/accounts"
const bodyString = ""
const secret = "71ad81f98fbbab22c9d74948d2899a65027208197291d11e2065c3a9c62fe1f0"
const timestamp = Math.floor(Date.now() / 1000) // "1540920260"

const stringToSign = timestamp + requestPath + bodyString
const hmac = Crypto.HmacSHA384(stringToSign, secret)
const signature = Base64.stringify(hmac)

console.log(signature)
//Q/U7zHlw5h8Ayk0iKQVdZJcjcBKD0xSMtuLGDPOEXFrx/SQ7UkkzM/ow731t815D
```

Para gerar uma assinatura do request, siga esses passos:

- Concatene: `timestamp` + `path do request` + `string do body`
- Crie um HMAC-SHA-384 na string obtida acima utilizando seu `secret`.
- Finalmente, converta o resultado para Base64

Exemplo de assinatura:

Campo | Valor 
----  | -----
**Path** | /accounts
**Body** | 
**Secret** | 71ad81f98fbbab22c9d74948d2899a65027208197291d11e2065c3a9c62fe1f0
**Timestamp** | 1540920260
**Assinatura** | Q/U7zHlw5h8Ayk0iKQVdZJcjcBKD0xSMtuLGDPOEXFrx/SQ7UkkzM/ow731t815D

### Idioma

```shell
curl -H "Content-Type: application/json" \
  -H "Accept-Language: en-US" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  https://api.swipetech.io/organizations/
```

Para configurar o idioma de resposta da API, utilize o seguinte header nas requisições:

- `Accept-Language`: [Idioma desejado](#idiomas-suportados) (`pt-BR` por padrão)

## SDK

Selecione a aba **javascript** ao lado para visualizar exemplos básicos de utilização do SDK.

### Instalação

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

Para fins de testes, disponibilizamos um ambiente de Sandbox.


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
* **Node:** Promise<[Data`<Organization`>](#data-lt-t-gt)>


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
  .then(list => 
    list.forEach(data => 
      console.log(data.receipt.id, data.value.id)
    )
  )
  .catch(error => 
    console.log(error)
  )
```

Busca informações sobre todas as Contas já criadas pela sua Organização.

`GET /accounts`

#### Retorno
* **API:** [AccountsResponse](#accountsresponse)
* **Node:** Promise<[Data\<Account\>[]](#data-lt-t-gt)>


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
  .then(data => 
    console.log(data.receipt.id, data.value.id)
  )
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
* **Node:** Promise<[Data\<Account\>](#data-lt-t-gt)>


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
  .then(list => 
    list.forEach(data => 
      console.log(data.receipt.id, data.value.code)
    )
  )
  .catch(error => 
    console.log(error)
  )
```

Busca todos os Ativos emitidos pela sua Organização.

`GET /assets`

#### Retorno
* **API:** [AssetsResponse](#assetsresponse)
* **Node:** Promise<[Data\<Asset\>](#data-lt-t-gt)>

### 5. Informações sobre uma Transferência

```shell
curl -X GET \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  https://api.swipetech.io/transfers/44d351a02f2307153be74984a59675f2733ad5deb1fa9fb08b0a36fe3d15fd6d
```

```javascript
swp.getTransfer("44d351a02f2307153be74984a59675f2733ad5deb1fa9fb08b0a36fe3d15fd6d")
  .then(data => 
    data.value.operations.forEach(op => 
      console.log(op.amount)
    )
  )
  .catch(error => 
    console.log(error)
  )
```

Busca informações sobre uma Transferência relacionada à sua Organização ou Conta filha. 

`GET /transfers/:id`

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
id | ID da Transferência 

#### Retorno
* **API:** [TransferResponse](#transferresponse)
* **Node:** Promise<[Data\<Transfer\>](#data-lt-t-gt)> 


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
  .then(data => 
    console.log(data.value.id)
  )
  .catch(error => 
    console.log(error)
  )
```

### 1. Criar nova Conta

`POST /accounts`

#### Retorno
* **API:** [AccountResponse](#accountresponse)
* **Node:** Promise<[Data\<Account\>](#data-lt-t-gt)>


### 2. Emitir um Ativo

<aside class="notice">No momento esta funcionalidade ainda não está aberta. Entre em contato conosco para mais detalhes.</aside>


### 3. Realizar uma Transferência

```shell
curl --request POST \
  -L https://api.sandbox.swipetech.io/transfers \
  -H 'accept: application/json' \
  -H 'accept-language: pt-BR' \
  -H 'content-type: application/json' \
  -H 'x-swp-api-key: <sua chave de api>' \
  -H 'x-swp-signature: <assinatura da requisição>' \
  -d '{"operations":[{"from":"269de13d714b253b88fdf18620c3194078f7932d48855efc6e4d6dc57528c84c","to":"b0ea341bd255aa27eb38ef136aebfcaaffbc87103d872a4a218df7b434f5a6ad","amount":121.22,"asset":"b6039b3fb9c3e30945644cc394e6b1accb0a6c2844514aad0819a89d64b0184c"}]}'
```

```javascript
// Perceba que é uma lista, mesmo que seja somente uma Operação na Transferência
swp.makeTransfer([
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
    
    // exibir índice e código de erro das operações que falharam
    error.sub_errors
      .forEach((se, i) => {
        // é possível checar qual a operação que falhou através de seu campo `index` 
        console.log(i, se.code, se.index)
      })
  })
```

`POST /transfers`

#### Body
[Transfer](#transfer)

#### Retorno
* **API:** [TransferResponse](#transferresponse)
* **Node:** Promise<[Data\<Transfer\>](#data-lt-t-gt)>
