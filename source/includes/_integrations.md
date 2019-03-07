# Integração

Mantemos um SDK oficial em [Node.js](https://github.com/Swipetech/swp-node-sdk) e lançamos suporte a outras linguagens conforme a demanda de nossos clientes.

Recomendamos a utilização de um SDK, pois eles abstraem boa parte da complexidade de integração, como autenticação, tratamento de erros e suporte a novas versões. Porém, como nossa API é baseada em REST, também é possível utilizá-la diretamente.

Ao longo desta seção, acompanhe na aba da direita exemplos para integração por meio da API REST ou por SDKs.

## Integração pela API REST

Selecione a aba **shell** ao lado para visualizar exemplos básicos de integração via API utilizando `curl`.

<aside class="warning">Lembre-se de incluir <code>https</code> em todas as suas requisições. Em caso de uma conexão não segura, a API retornará um erro com código <code>insecure_connection</code>.</aside>

### Ambientes

Use os domínios respectivos para suas requisições:

- **Produção:** `https://api.swipetech.io`
- **Sandbox:** `https://api-sandbox.swipetech.io`

### Autenticação

Só é necessário se preocupar com a autenticação das chamadas caso opte por integrar diretamente à nossa API.
Nosso SDK abstrai completamente essa complexidade.

#### Headers

```shell
curl -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <API_KEY>" \
  -H "X-Swp-Signature: <SIGNATURE>" \
  https://api.swipetech.io/organizations
```

Utilizamos um modelo de `API Key` e `Secret` para autenticar as requisições.
Todas devem incluir os seguintes headers:

- `X-Swp-Timestamp`: Número de segundos desde [Unix Epoch](https://en.wikipedia.org/wiki/Unix_time).
Aceita-se uma margem de 5 minutos de diferença com o horário do recebimento da requisição pela API.
- `X-Swp-Api-Key`: API Key como string
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

Para gerar uma assinatura do request, siga esses passos, acompanhando a aba **javascript** ao lado:

- Concatene: `timestamp` + `path do request` + `string do body`
- Crie um HMAC-SHA-384 a partir da string obtida acima utilizando seu `secret`
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
  https://api.swipetech.io/organizations
```

Para configurar o idioma de resposta da API, utilize o seguinte header nas requisições:

- `Accept-Language`: [Idioma desejado](#idiomas-suportados) (`pt-BR` por padrão)

## Integração por SDK

Selecione a aba **javascript** ao lado para visualizar exemplos básicos de utilização do SDK.

### Instalação

```javascript
// ES2015 ou TypeScript
import * as Swipe from '@swp/swipe-sdk'
```

```javascript
// CommonJS
const Swipe = require('@swp/swipe-sdk')
```

Via npm:

`npm i @swp/swipe-sdk`

Via yarn:

`yarn add @swp/swipe-sdk`

<aside class="warning"><b>Atenção:</b> a integração deve ser realizada sempre a partir de um servidor Node.js, nunca a partir de um navegador. Inicializar o SDK JavaScript a partir de um navegador poderá expor indevidamente seu <b>API Key</b> e <b>Secret</b>, mesmo que sejam utilizadas práticas de <i>code obfuscation</i>.</aside>

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
  language: Swipe.languages.EN_US,
})
```

Após a [instalação](#sdks), o primeiro passo é inicializar o SDK com uma `API Key`, um `Secret` e um `Idioma` válidos.

Para fins de testes, disponibilizamos um ambiente de Sandbox. Veja ao lado um exemplo.

## Lidando com valores nos Ativos

Existem algumas regras para lidar com valores nos Ativos:

- Sempre serão do tipo `String`.

- O valor máximo emitido para um Ativo ou de uma Transferência é de 920_000_000_000 (920 bilhões).

- O valor mínimo emitido para um Ativo ou de uma Transferência é de 0.0000001. Assim, a precisão máxima é de 7 casas decimais.

<aside class="warning">Algumas linguagens de programação (Javascript, por exemplo) possuem problemas em manter a precisão em campos numéricos. Recomendamos utilizar alguma biblioteca que lide com <code>Big Numbers</code> e possa lidar com valores de precisão arbitrários sem perda.</aside>

## Paginação

```shell
curl -X GET \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  https://api.swipetech.io/accounts?limit=10&starting_after=10003
```

```javascript
swp.getAllAccounts({limit: "10"})
  .then(({data, pagination}) => {
    data.forEach(({ receipt, value }) => {
      console.log(receipt.id)
      console.log(value.id)
    })

    // carregar a segunda página
    return swp.getAllAccounts({ limit: "10", starting_after: pagination.cursor})
  })
  .then(({data}) =>
    data.forEach(({ receipt, value }) => {
      console.log(receipt.id)
      console.log(value.id)
    })
  )
```

Utilizamos um modelo de paginação baseada em *[Cursor](https://slack.engineering/evolving-api-pagination-at-slack-1c1f644f8e12)*.
Os seguintes endpoints a suportam:

- Buscar todas as Contas (GET /accounts)
- Buscar todos os Ativos (GET /assets)
- Buscar todas as Transferências (GET /transfers)

Para utilizar a paginação, existem dois parâmetros opcionais que são passados via *[query parameters](https://branch.io/glossary/query-parameters/)* na url da requisição:

- `limit`: Limite de ítens para a resposta. (Se omitido, seu valor padrão é de 100)
- `starting_after`: Utilizado para buscar os próximos ítens em uma nova requisição, a partir de um valor de `cursor`.

<aside class="notice">PS: Todos os endpoints que devolvem uma lista são ordenados do mais novo ao mais antigo. A resposta é sempre uma <a href="#successresponse-lt-t-gt">SuccessResponse</a>, que contêm um campo do tipo <a href="#pagination">Pagination</a>.</aside>

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
  .then(({data}) => console.log(data.value.name))
  .catch(error =>
    console.log(error)
  )
```

Busca informações sobre sua Organização.

`GET /organizations`

#### Retorno
* **API:** [SuccessResponse](#successresponse-lt-t-gt)<[Data](#data-lt-t-gt)<[Organization](#organization)>>
* **Node:** Promise<[Data](#data-lt-t-gt)<[Organization](#organization)>>


### 2. Todas as Contas

```shell
curl -X GET \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  https://api.swipetech.io/accounts?limit=10
```

```javascript
swp.getAllAccounts({limit: "10"})
  .then(({data, pagination}) => {
    data.forEach(({ receipt, value }) => {
      console.log(receipt.id)
      console.log(value.id)
    })

    // carregando a segunda página
    return swp.getAllAccounts({ limit: "10", starting_after: pagination.cursor})
  })
  .then(({data}) =>
    data.forEach(({ receipt, value }) => {
      console.log(receipt.id)
      console.log(value.id)
    })
  )
  .catch(error =>
    console.log(error)
  )
```

Busca informações sobre todas as Contas já criadas pela sua Organização.

`GET /accounts?limit=<limit>&starting_after=<starting_after>`

#### Parâmetros de Query

Parâmetro | Descrição
--------- | -----------
limit | (opcional) Limite de itens por página
starting_after | (opcional) ID do item a partir do qual a pagina deve começar

#### Retorno
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<Array<[Data](#data-lt-t-gt)<[Account](#account)>>>
* **Node:** Promise\<Array\<[Data](#data-lt-t-gt)\<[Account](#account)\>\>\>


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
  .then(({data}) =>
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
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)<[Account](#account)>>
* **Node:** Promise\<[Data](#data-lt-t-gt)\<[Account](#account)\>\>


### 4. Todos os Ativos

```shell
curl -X GET \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  https://api.swipetech.io/assets?limit=10
```

```javascript
swp.getAllAssets({limit: "10"})
  .then(({ data, pagination }) => {
    data.forEach(({ receipt, value }) => {
      console.log(receipt.id)
      console.log(value.code)
    })

    // carregando a segunda página
    return swp.getAllAssets({ limit: "10", starting_after: pagination.cursor })
  })
  .then(({data}) =>
    data.forEach(({ receipt, value }) => {
      console.log(receipt.id)
      console.log(value.code)
    })
  )
  .catch(error =>
    console.log(error)
  )
```

Busca todos os Ativos emitidos pela sua Organização.

`GET /assets?limit=<limit>&starting_after=<starting_after>`

#### Parâmetros de Query

Parâmetro | Descrição
--------- | -----------
limit | (opcional) Limite de itens por página
starting_after | (opcional) ID do item a partir do qual a pagina deve começar

#### Retorno
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<Array<[Data](#data-lt-t-gt)<[Asset](#asset)>>>
* **Node:** Promise\<Array\<[Data](#data-lt-t-gt)\<[Asset](#asset)\>\>\>

### 5. Todas as Transferências relativas a uma Conta

```shell
curl -X GET \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  https://api.swipetech.io/accounts/44d351a02f2307153be74984a59675f2733ad5deb1fa9fb08b0a36fe3d15fd6d/transfers?limit=10
```

```javascript
const accountId = "44d351a02f2307153be74984a59675f2733ad5deb1fa9fb08b0a36fe3d15fd6d"

swp.getAllTransfers(accountId, {limit: "10"})
  .then(({ data, pagination }) => {
    data.forEach(({ receipt, value }) => {
      console.log(receipt.id)
      console.log(value.amount)
    })

    // carregando a segunda página
    return swp.getAllTransfers(accountId, { limit: "10", starting_after: pagination.cursor })
  })
  .then(({data}) =>
    data.forEach(({ receipt, value }) => {
      console.log(receipt.id)
      console.log(value.amount)
    })
  )
  .catch(error =>
    console.log(error)
  )
```

Busca todas as Transferências relacionadas à sua Organização ou Conta filha, **incluindo transferências enviadas e recebidas**.

`GET /accounts/:id/transfers?limit=<limit>&starting_after=<starting_after>`

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
id | ID da Conta ou da Organização

#### Parâmetros de Query

Parâmetro | Descrição
--------- | -----------
limit | (opcional) Limite de itens por página
starting_after | (opcional) ID do item a partir do qual a pagina deve começar

#### Retorno
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<Array\<[Data](#data-lt-t-gt)\<[Transfer](#transfer)>>>
* **Node:** Promise\<Array\<[Data](#data-lt-t-gt)\<[Transfer](#transfer)>>>

### 6. Um lote de Transferências específico

```shell
curl -X GET \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  https://api.swipetech.io/transfers/44d351a02f2307153be74984a59675f2733ad5deb1fa9fb08b0a36fe3d15fd6d
```

```javascript
swp.getTransfer("44d351a02f2307153be74984a59675f2733ad5deb1fa9fb08b0a36fe3d15fd6d")
  .then(({data}) =>
    data.value.actions.forEach(tf =>
      console.log(tf.amount)
    )
  )
  .catch(error =>
    console.log(error)
  )
```

Busca informações sobre um lote de Transferências relacionadas à sua Organização ou Conta filha.

`GET /transfers/:id`

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
id | ID do lote de Transferências

#### Retorno
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[TransferBatch](#transferbatch)>>
* **Node:** Promise\<[Data](#data-lt-t-gt)\<[TransferBatch](#transferbatch)>>


## Executar ações

### 1. Criar nova Conta

```shell
curl -X POST \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  https://api.swipetech.io/accounts
```

```javascript
swp.createAccount({
  // Este campo é opcional. Por padrão todas as Contas filhas suportam todos os Ativos da Organização e possuem saldo zero.
  starting_balances: [
    {
      asset_id: '07773f06becd47385d1e8d1e9bad3bd588ccd880fe746819257a6246e33551d3',
      balance: '1.99'
    }
  ]
})
.then(({data}) =>
  console.log(data.value.id)
)
.catch(error =>
  console.log(error)
)
```

`POST /accounts`

#### Body
[NewAccount](#newaccount)

#### Retorno
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[Account](#account)>>
* **Node:** Promise\<[Data](#data-lt-t-gt)\<[Account](#account)>>


### 2. Emitir um Ativo

```shell
curl -X POST \
  https://api.swipetech.io/assets \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  -d '{"code": "TOKEN", "limit": "10000000"}'
```

```javascript
swp.issueAsset({
  code: "TOKEN",
  limit: "10000000"
})
  .then(({data}) =>
    console.log(data.value.id)
  )
  .catch(error =>
    console.log(error)
  )
```

`POST /assets`

#### Body
[NewAsset](#newasset)

#### Retorno
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[Asset](#asset)>>
* **Node:** Promise\<[Data](#data-lt-t-gt)\<[Asset](#asset)\>\>

### 3. Realizar Transferências em lote

```shell
curl --request POST \
  -L https://api-sandbox.swipetech.io/transfers \
  -H 'accept: application/json' \
  -H 'accept-language: pt-BR' \
  -H 'content-type: application/json' \
  -H 'x-swp-api-key: <sua chave de api>' \
  -H 'x-swp-signature: <assinatura da requisição>' \
  -d '{"transfers":[{"from":"269de13d714b253b88fdf18620c3194078f7932d48855efc6e4d6dc57528c84c","to":"b0ea341bd255aa27eb38ef136aebfcaaffbc87103d872a4a218df7b434f5a6ad","amount":"121.22","asset":"b6039b3fb9c3e30945644cc394e6b1accb0a6c2844514aad0819a89d64b0184c"}],"memo":"01234567"}'
```

```javascript
// Note que é uma lista, mesmo que haja somente uma Transferência
swp.makeTransfers({
  actions: [
    {
      from: "44d351a02f2307153be74984a59675f2733ad5deb1fa9fb08b0a36fe3d15fd6d",
      to: "55c86a9027f2ff8c5d6ed1e2dbda01886b8b33f461341533d7391c14abe7aa40",
      asset: "07773f06becd47385d1e8d1e9bad3bd588ccd880fe746819257a6246e33551d3",
      amount: "1000",
    }
  ],
  memo: "01234567"
})
  .then(({data}) => {
    data.value.actions.forEach(tf => {
      console.log(tf.amount)
    })
  })
  .catch(error => {
    console.log(error)

    // exibir índice e código de erro das transferências que falharam
    error.sub_errors
      .forEach((se, i) => {
        // é possível checar qual a operação que falhou através de seu campo `index`
        console.log(i, se.code, se.index)
      })
  })
```

Executa uma lista de Ações do tipo Transferência de forma atômica, isto é, se uma falhar, todas falharão.

Obs: O campo `memo` pode ser utilizado para salvar informações na [rede](#blockchain-e-dlt). Ele é opcional.

`POST /transfers`

#### Body
[TransferBatch](#transferbatch)

#### Retorno
* **API:** [SuccessResponse](#response-lt-t-gt)\<[Data](#data-lt-t-gt)\<[TransferBatch](#transferbatch)>>
* **Node:** Promise<[Data](#data-lt-t-gt)\<[TransferBatch](#transferbatch)>>

### 4. Destruir uma Conta

```shell
curl -X DELETE \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  https://api.swipetech.io/accounts/44d351a02f2307153be74984a59675f2733ad5deb1fa9fb08b0a36fe3d15fd6d
```

```javascript
swp.destroyAccount(accountID)
  .then(({data}) =>
    console.log(data.value.id)
  )
  .catch(error =>
    console.log(error)
  )
```

Destrói uma Conta. O único requisito é que ela tenha saldo zero para todos seus Ativos.

`DELETE /accounts/:id`

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
id | ID da Conta

#### Retorno
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[Account](#account)>>
* **Node:** Promise\<[Data](#data-lt-t-gt)\<[Account](#account)>>

### 5. Ações em lote

```shell
curl -X POST \
  https://api.swipetech.io/actions \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  -d '{"actions": [{"type": "CREATE_ACC"}, {"type": "ISSUE_ASSET", "code": "TOKEN", "limit": "10000"}, {"type": "TRANSFER", "from": "269de13d714b253b88fdf18620c3194078f7932d48855efc6e4d6dc57528c84c", "to": "b0ea341bd255aa27eb38ef136aebfcaaffbc87103d872a4a218df7b434f5a6ad", "asset": "b6039b3fb9c3e30945644cc394e6b1accb0a6c2844514aad0819a89d64b0184c", "amount": "100"}], "memo": "Memo"}'
```

```javascript
swp.makeActionBatch({
  actions: [
    {
      type: "CREATE_ACC"
    },
    {
      type: "ISSUE_ASSET",
      code: "TOKEN",
      limit: "10000"
    },
    {
      type: "TRANSFER",
      from: "269de13d714b253b88fdf18620c3194078f7932d48855efc6e4d6dc57528c84c",
      to: "b0ea341bd255aa27eb38ef136aebfcaaffbc87103d872a4a218df7b434f5a6ad",
      asset: "b6039b3fb9c3e30945644cc394e6b1accb0a6c2844514aad0819a89d64b0184c",
      amount: "100"
    }
  ],
  memo: "Memo"
})


// Também é possível usar funções auxiliares
// para construir as actions
import { createAccountAction, issueAssetAction, transferAction } from "@swp/swipe-sdk"

swp.makeActionBatch({
  actions: [
    createAccountAction(),
    issueAssetAction({
      code: "TOKEN",
      limit: "10000"
    }),
    transferAction({
      from: "269de13d714b253b88fdf18620c3194078f7932d48855efc6e4d6dc57528c84c",
      to: "b0ea341bd255aa27eb38ef136aebfcaaffbc87103d872a4a218df7b434f5a6ad",
      asset: "b6039b3fb9c3e30945644cc394e6b1accb0a6c2844514aad0819a89d64b0184c",
      amount: "100"
    })
  ]
})
```

Executa um [lote de Ações](#lote-de-acoes)

Obs: o campo `memo` pode ser utilizado para salvar informações na [rede](#blockchain-e-dlt). Ele é opcional.

`POST /actions`

#### Body
[ActionBatch](#actionbatch)

#### Retorno
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[ActionBatch](#actionbatch)\>
* **Node:** Promise\<[Data](#data-lt-t-gt)\<[ActionBatch](#account)\>\>

## Tags

Para fins organizacionais, uma Conta filha pode conter uma ou mais Tags.

### 1. Especificar Tags na criação de Conta ou Ativo

```shell
curl -X POST \
  -L https://api.swipetech.io/accounts \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  -d '{"tags":["fornecedor"]}'

curl -X POST \
  -L https://api.swipetech.io/assets \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  -d '{"code": "TOKEN", "tags":["fornecedor"]}'
```

```javascript
swp.createAccount({tags: ["fornecedor"]})
  .then(({data}) =>
    console.log(data.value.tags)
  )
  .catch(error =>
    console.log(error)
  )

swp.issueAsset({
  code: "TOKEN",
  tags: ["fornecedor"]
})
  .then(({data}) =>
    console.log(data.value.tags)
  )
  .catch(error =>
    console.log(error)
  )
```

As Ações de [criação de Conta](#executar-acoes) e de [emissão de um Ativo](#executar-acoes) recebem um parâmetro opcional chamado `tags`.
Esse campo é opcional e pode ser utilizado para categorizar ou segmentar um ou mais conjunto de Contas ou Ativos.

Cada Conta / Ativo pode conter, no máximo, 10 tags.

Cada tag possui um limite de 200 caracteres. São válidos:

- `a-z` -> com diferenciação entre maiúsculas e minúsculas
- `0-9`
- `-`
- `_`
- `\`
- `/`
- `|`
- `:`

### 2. Alterar Tags

```shell
curl -X PUT \
  -L https://api.swipetech.io/tags/44d351a02f2307153be74984a59675f2733ad5deb1fa9fb08b0a36fe3d15fd6d \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  -d '{"tags":["cliente"]}'
```

```javascript
const id = '44d351a02f2307153be74984a59675f2733ad5deb1fa9fb08b0a36fe3d15fd6d'

swp.updateTags(id, ["cliente"])
  .then(({data}) =>
    console.log(data.value.tags)
  )
  .catch(error =>
    console.log(error)
  )
```

`PUT /tags/:id`

Remove as Tags de uma Conta ou Ativo e as substitui por um novo conjunto.

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
id | ID da Conta ou Ativo

#### Body
[NewTags](#newtags)

#### Retorno
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[Tags](#tags)>>
* **Node:** Promise\<[Data](#data-lt-t-gt)\<[Tags](#tags)>>

### 3. Filtrar Contas por Tag

```shell
# Filtrando Contas por tag
curl -X GET \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  https://api.swipetech.io/accounts?tag=fornecedor
```

```javascript
// Filtrando Contas por tag
swp.getAllAccounts({ tag: "fornecedor" })
  .then(({data}) =>
    data.forEach(({value, receipt}) => {
      console.log(value.id)
      console.log(receipt.id)
    })
  )
  .catch(error =>
    console.log(error)
  )
```

Filtra Contas que contém uma tag específica.

`GET /accounts?tag=<tag>`

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
tag | Tag para filtragem

#### Retorno
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[Account](#account)>>
* **Node:** Promise\<Array\<[Data](#data-lt-t-gt)\<[Account](#account)>>>

### 4. Filtrar Ativos por Tag

```shell
curl -X GET \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  https://api.swipetech.io/assets?tag=fornecedor
```

```javascript
swp.getAllAssets({ tag: "cripto" })
  .then(({data}) =>
    data.forEach(({value, receipt}) => {
      console.log(value.id)
      console.log(receipt.id)
    })
  )
  .catch(error =>
    console.log(error)
  )
```

Filtra Ativos que contém uma tag específica.

`GET /assets?tag=<tag>`

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
tag | Tag para filtragem

#### Retorno
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[Asset](#asset)>>
* **Node:** Promise\<Array\<[Data](#data-lt-t-gt)\<[Asset](#asset)>>>

## Outros

### 1. Resetar Organização (Disponível apenas para Sandbox)

```shell
curl -X DELETE \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  https://api.swipetech.io/organizations
```

```javascript
swp.resetOrganization()
  .then(() =>
    console.log("feito!!!")
  )
  .catch(error =>
    console.log(error)
  )
```
Esta função tem como objetivo retornar a Organização para seu estado inicial.

#### Isto é:
* Todas as Contas pertencentes à Organização serão removidas.
* Todo o saldo será retornado à Organização.
* Os IDs da Organização e Ativos serão trocados.

`DELETE /organizations`

<aside class="warning">Essa Ação é destrutiva e não pode ser desfeita.</aside>

### 2. Revoke de um par de Credenciais

Essa ação ocorre em 2 passos.

```shell
# Busca o token de Revoke
curl -X GET \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  https://api.swipetech.io/organizations/revoke

# Efetiva o Revoke  
curl -X POST \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  https://api.swipetech.io/organizations/revoke/eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJUeXBlIjoiUkVWT0tFIENSRURFTlRJQUxTIiwiZXhwIjoxNTUwODYyNjY2LCJUaW1lc3RhbXAiOjE1NTA4NjIzNjZ9.s9UbrJmWQXVpIeXAb9gjWwRe19iWV1gYIaoxXOQ0_1A
```

```javascript
swp.getToken()
  .then(({data}) => {
      console.log(data.value.token)
      // eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJUeXBlIjoiUkVWT0tFIENSRURFTlRJQUxTIiwiZXhwIjoxNTUwODYyNjY2LCJUaW1lc3RhbXAiOjE1NTA4NjIzNjZ9.s9UbrJmWQXVpIeXAb9gjWwRe19iWV1gYIaoxXOQ0_1A

      return swp.revokeCredentials(data.value.token)
  })
  .then(() => console.log("Revoke efetuado com sucesso"))
  .catch(error =>
    console.log(error)
  )
```

O primeiro é buscar um token de Revoke (valido por 5 min):

`GET /organizations/revoke`

#### Retorno
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[ResponseToken](#responsetoken)>>
* **Node:** Promise\<[Data](#data-lt-t-gt)\<[ResponseToken](#responsetoken)>>

E, em seguida, utilizar o token obtido no passo anterior para executar o Revoke:

`POST /organizations/revoke/:token`

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
token | token de Revoke obtido no primeiro passo

#### Retorno
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<null>>
* **Node:** Promise\<[Data](#data-lt-t-gt)\<null>>

<aside class="warning">Essa Ação é destrutiva e não pode ser desfeita.</aside>
