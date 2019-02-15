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

`https://api-sandbox.swipetech.io`

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
Todos os endpoints que retornam uma lista de recursos a suportam:

- Buscar todas as Contas (GET /accounts)
- Buscar todos os Ativos (GET /assets)
- Buscar todas as Transferências (GET /transfers)

Para utilizar a paginação, existem dois parâmetros opcionais que são passados via *[query parameters](https://branch.io/glossary/query-parameters/)* na url da requisição:

- `limit`: Limite de ítens para a resposta. (Se omitido, seu valor padrão é de 100)
- `starting_after`: Utilizado para buscar os próximos ítens em uma nova requisição, a partir de um valor de `cursor`.

<aside class="notice">PS: Todos os endpoints que devolvem uma lista são ordenados do mais novo ao mais antigo. A resposta é sempre uma <a href="#responselist-lt-t-gt">ResponseList</a>, que contêm um campo do tipo <a href="#pagination">Pagination</a>.</aside>

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
* **API:** [Response](#response-lt-t-gt)<[Organization](#organization)>
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

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
limit | (opcional) Limite de itens por página
starting_after | (opcional) ID do item a partir do qual a pagina deve começar

#### Retorno
* **API:** [ResponseList](#responselist-lt-t-gt)\<[Account](#account)\>
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
* **API:** [Response](#response-lt-t-gt)\<[Account](#account)\>
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
    data.forEach(() => {
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

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
limit | (opcional) Limite de itens por página
starting_after | (opcional) ID do item a partir do qual a pagina deve começar

#### Retorno
* **API:** [ResponseList](#responselist-lt-t-gt)\<[Asset](#asset)\>
* **Node:** Promise\<Array\<[Data](#data-lt-t-gt)\<[Asset](#asset)\>\>\>

### 5. Todas as Transferências relativas a uma conta

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
    return swp.getAllTransfer(accountId, { limit: "10", starting_after: pagination.cursor })
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
id | (opcional) ID da Conta ou da Organização
limit | (opcional) Limite de itens por página
starting_after | (opcional) ID do item a partir do qual a pagina deve começar

#### Retorno
* **API:** [ResponseList](#responselist-lt-t-gt)\<[TransferOperation](#transferoperation)\>
* **Node:** Promise\<Array\<[Data](#data-lt-t-gt)\<[TransferOperation](#transferoperation)\>\>\>

### 6. Uma Transferência específica

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
* **API:** [Response](#response-lt-t-gt)\<[Transfer](#transfer)\>
* **Node:** Promise\<[Data](#data-lt-t-gt)\<[Transfer](#transfer)\>\>


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
swp.createAccount()
  .then(data =>
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
* **API:** [Response](#response-lt-t-gt)\<[Account](#account)\>
* **Node:** Promise\<[Data](#data-lt-t-gt)\<[Account](#account)\>\>


### 2. Emitir um Ativo

<aside class="notice">No momento esta funcionalidade ainda não está aberta. Entre em contato conosco para mais detalhes.</aside>


### 3. Realizar uma Transferência

```shell
curl --request POST \
  -L https://api-sandbox.swipetech.io/transfers \
  -H 'accept: application/json' \
  -H 'accept-language: pt-BR' \
  -H 'content-type: application/json' \
  -H 'x-swp-api-key: <sua chave de api>' \
  -H 'x-swp-signature: <assinatura da requisição>' \
  -d '{"operations":[{"from":"269de13d714b253b88fdf18620c3194078f7932d48855efc6e4d6dc57528c84c","to":"b0ea341bd255aa27eb38ef136aebfcaaffbc87103d872a4a218df7b434f5a6ad","amount":"121.22","asset":"b6039b3fb9c3e30945644cc394e6b1accb0a6c2844514aad0819a89d64b0184c"}],"memo":"01234567"}'
```

```javascript
// Note que é uma lista, mesmo que haja somente uma Operação na Transferência
swp.makeTransfer({
  operations: [
    {
      from: "44d351a02f2307153be74984a59675f2733ad5deb1fa9fb08b0a36fe3d15fd6d",
      to: "55c86a9027f2ff8c5d6ed1e2dbda01886b8b33f461341533d7391c14abe7aa40",
      asset: "07773f06becd47385d1e8d1e9bad3bd588ccd880fe746819257a6246e33551d3",
      amount: "1000",
    }
  ], 
  memo: "01234567"
})
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

Obs: O campo `memo` pode ser utilizado para salvar informações na [rede](#blockchain-e-dlt). Ele é opcional. 

`POST /transfers`

#### Body
[Transfer](#transfer)

#### Retorno
* **API:** [Response](#response-lt-t-gt)\<[Transfer](#transfer)\>
* **Node:** Promise<[Data](#data-lt-t-gt)\<[Transfer](#transfer)\>\>

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
  .then(data =>
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
id | ID da Conta a ser destruída

#### Retorno
* **API:** [Response](#response-lt-t-gt)\<[Account](#account)\>
* **Node:** Promise\<[Data](#data-lt-t-gt)\<[Account](#account)\>\>

<aside class="warning">Essa Ação é destrutiva e não pode ser desfeita.</aside>

## Tags

Para fins organizacionais, uma Conta filha pode conter uma ou mais Tags.

### 1. Especificar Tags na criação de uma nova Conta

```shell
curl -X POST \
  -L https://api.swipetech.io/accounts \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  -d '{"tags":["fornecedor"]}'
```

```javascript
swp.createAccount({tags: ["fornecedor"]})
  .then(data =>
    console.log(data.value.tags)
  )
  .catch(error =>
    console.log(error)
  )
```

Cria uma nova Conta especificando uma ou mais Tags.

`POST /accounts`

#### Body
[NewAccount](#newaccount)

#### Retorno
* **API:** [Response](#response-lt-t-gt)\<[Account](#account)\>
* **Node:** Promise\<[Data](#data-lt-t-gt)\<[Account](#account)\>\>

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
  .then(data =>
    console.log(data.value.tags)
  )
  .catch(error =>
    console.log(error)
  )
```

`PUT /tags/:id`

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
id | ID da entidade a ser atualizada com novas Tags

#### Body
[NewTags](#newtags)

#### Retorno
* **API:** [Response](#response-lt-t-gt)\<[Tags](#tags)\>
* **Node:** Promise\<[Data](#data-lt-t-gt)\<[Tags](#tags)\>\>

### 3. Filtrar Contas por Tag

```shell
# Filtrando Contas por tag
curl -X GET \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  https://api.swipetech.io/accounts?tag=fornecedor

# Filtrando Assets por tag
curl -X GET \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  https://api.swipetech.io/assets?tag=fornecedor
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

// Filtrando Assets por tag
swp.getAllAssets({ tag: "cripto" })
  .then(({data}) =>
    data.forEach(({value, receipt}) =>
      console.log(value.id)
      console.log(receipt.id)
    )
  )
  .catch(error =>
    console.log(error)
  )
```

Filtra entidades que contém uma tag específica.

`GET /accounts?tag=<tag>`

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
tag | Tag para filtragem

#### Retorno
* **API:** [ResponseList](#responselist-lt-t-gt)\<[Account](#account)\>
* **Node:** Promise\<Array\<[Data](#data-lt-t-gt)\<[Account](#account)\>\>\>

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
    console.log("done!!!")
  )
  .catch(error =>
    console.log(error)
  )
```
Esta função tem como objetivo retornar a organização para seu estado inicial. 
#### Isto é: 
* todas as contas pertencentes à organização serão removidas.
* todo o saldo será retornado à organização.
* os IDs da organização e ativos serão trocados. 

`DELETE /organizations`

<aside class="warning">Essa Ação é destrutiva e não pode ser desfeita.</aside>
