# Integração

Mantemos um SDK oficial em [Node.js v0.9.1](https://github.com/Swipetech/swp-node-sdk) e outro em [Java/Kotlin v0.9.1](https://github.com/swipetech/swp-java-sdk) e lançamos suporte a outras linguagens conforme a demanda de nossos clientes.

Recomendamos fortemente a utilização de um SDK, pois eles abstraem boa parte da complexidade de integração, como autenticação, tratamento de erros e suporte a novas versões.

Como nossa API é baseada em REST, também é possível utilizá-la diretamente.

Ao longo desta seção, acompanhe na aba da direita exemplos para integração usando SDKs ou por meio de nossa API REST.

## Integração por SDK

Selecione as abas ao lado para visualizar exemplos básicos de utilização do SDK em cada linguagem.

### Instalação e Inicialização

```javascript
/* 

Via npm:

`npm i @swp/swipe-sdk`

Via yarn:

`yarn add @swp/swipe-sdk`

*/
```
```javascript
// ES2015 ou TypeScript
import * as Swipe from '@swp/swipe-sdk'
```

```javascript
// ou CommonJS
const Swipe = require('@swp/swipe-sdk')
```

```java
/*

Gradle
    
//Step 1. Add it in your root build.gradle at the end of repositories:

allprojects {
  repositories {
    ...
    maven { url 'https://jitpack.io' }
  }
} 
  	
//Step 2. Add the dependency

dependencies {
    implementation 'com.github.swipetech:swp-java-sdk:0.9.1'
}
 

Maven

//Step 1. Add the JitPack repository to your build file 

<repositories>
  <repository>
      <id>jitpack.io</id>
      <url>https://jitpack.io</url>
  </repository>
</repositories> 
	
	
//Step 2. Add the dependency

<dependency>
    <groupId>com.github.swipetech</groupId>
    <artifactId>swp-java-sdk</artifactId>
    <version>0.9.1</version>
</dependency>

 */
```

<aside class="warning"><b>Atenção:</b> a integração deve ser realizada sempre a partir de um servidor Node.js, nunca a partir de um navegador. Inicializar o SDK JavaScript a partir de um navegador poderá expor indevidamente seu <b>API Key</b> e <b>Secret</b>, mesmo que sejam utilizadas práticas de <i>code obfuscation</i>.</aside>

```javascript
// Inicia no ambiente de Produção
const swp = Swipe.init({
  apiKey: "your api key",
  secret: "your secret key",
  language: Swipe.languages.PT_BR,
})
```



```javascript
// Inicia no ambiente de Sandbox
const swp = Swipe.init({
  apiKey: "your api key",
  secret: "your secret key",
  sandbox: true,
  language: Swipe.languages.PT_BR,
})
```

```java
// Inicia no ambiente de Produção
 Swipe swp = new SwpBuilder()
                 .setApiKey("your api key")
                 .setSecret("your secret key")
                 .setLang(AcceptLanguage.PT_BR)
                 .build();
```

```java
// Inicia no ambiente de Sandbox
 Swipe swp = new SwpBuilder()
                 .setApiKey("your api key")
                 .setSecret("your secret key")
                 .setLang(AcceptLanguage.PT_BR)
                 .setEnvAsSandbox()
                 .build();
```

Após a instalação, o primeiro passo é inicializar o SDK com uma `API Key`, um `Secret` e um `Idioma` válidos.

Para fins de testes, disponibilizamos um ambiente de Sandbox. Veja ao lado um exemplo.

## Integração pela API REST

Selecione a aba **shell** ao lado para visualizar exemplos básicos de integração via API utilizando `curl`.

<aside class="warning">Lembre-se de incluir <code>https</code> em todas as suas requisições. Em caso de uma conexão não segura, a API retornará um erro com código <code>insecure_connection</code>.</aside>

### Ambientes

Use os domínios a seguir para suas requisições.

- **Produção:** `https://0-9.api.swipetech.io`
- **Sandbox:** `https://api-sandbox.swipetech.io`

### Autenticação

Note que só é necessário se preocupar com a autenticação das requisições caso opte por integrar diretamente à nossa API.
A integração por SDK abstrai completamente essa complexidade.

#### Headers

```shell
curl -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <API_KEY>" \
  -H "X-Swp-Signature: <SIGNATURE>" \
  https://api.swipetech.io/organizations
```

Utilizamos um modelo de `API Key` e `Secret` para autenticar as requisições.
Todas as requisições devem incluir os seguintes headers:

- `X-Swp-Timestamp`: Número de segundos desde [Unix Epoch](https://en.wikipedia.org/wiki/Unix_time).
Aceita-se uma margem de 5 minutos de diferença com o horário do recebimento da requisição pela API.
- `X-Swp-Api-Key`: API Key como string
- `X-Swp-Signature`: Assinatura da requisição (explicado abaixo)

#### Assinatura (X-Swp-Signature)

```javascript
const Crypto = require("crypto-js")
const Base64 = require("crypto-js/enc-base64")
const requestPath = "/accounts"
const bodyString = ""
const secret = "71ad81f98fbbab22c9d74948d2899a65027208197291d11e2065c3a9c62fe1f0"
const timestamp = Math.floor(Date.now() / 1000) // "1540920260"
const method = "GET"

const stringToSign = method + timestamp + requestPath + bodyString
const hmac = Crypto.HmacSHA256(stringToSign, secret)
const signature = Base64.stringify(hmac)

console.log(signature)
// /h02U+jDJWOG1npOvL1pH79hgGWT7mWymzxISP5IphA=
```

Para gerar uma assinatura da requisição, siga os passos a seguir, acompanhando a aba **javascript** ao lado:

- Concatene: `method` + `timestamp` + `path do request` + `string do body`
- Utilize seu `secret` para criar um HMAC-SHA-256 a partir da string obtida acima
- Finalmente, converta o resultado para Base64

Exemplo de assinatura:

Campo | Valor
----  | -----
**Path** | /accounts
**Method** | GET
**Body** |
**Secret** | 71ad81f98fbbab22c9d74948d2899a65027208197291d11e2065c3a9c62fe1f0
**Timestamp** | 1540920260
**Signature** | /h02U+jDJWOG1npOvL1pH79hgGWT7mWymzxISP5IphA=

### Idioma

```shell
curl -H "Content-Type: application/json" \
  -H "Accept-Language: pt-BR" \
  -H "X-Swp-Api-Key: <sua api key>" \
  -H "X-Swp-Signature: <assinatura da requisição>" \
  https://api.swipetech.io/organizations
```

Para configurar o idioma de resposta da API, utilize o seguinte header nas requisições:

- `Accept-Language`: [Idioma desejado](#idiomas-suportados) (`pt-BR` por padrão)

## Formatação dos valores de Ativos

É preciso observar as seguintes regras de formatação para os valores de Ativos.

- Deverão ser sempre do tipo `string`.

- O valor máximo emitido para um Ativo ou de uma Transferência é de 920_000_000_000 (920 bilhões).

- O valor mínimo emitido para um Ativo ou de uma Transferência é de 0.0000001. Assim, a precisão máxima é de 7 casas decimais.

<aside class="warning">Algumas linguagens de programação (como Javascript) podem ter problemas para manter a precisão em campos numéricos. Recomendamos utilizar alguma biblioteca que trabalhe com <b>Big Numbers</b> e possa lidar com valores de precisão arbitrária sem perdas.</aside>

## Buscar informações

Nesta seção estão listados os endpoints usados para buscar mais detalhes sobre uma [Organização](#organizacao), [Contas](#conta) ou [Ativos](#ativo).

### Paginação para requisições GET 

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

```java 

    SuccessResponse<List<DataDTOReceipt<AccountDTO>>> resp =
    swp.getAllAccounts(
        new PaginationParams(null, "3"), null
    );

    PaginationResponse pagination = resp.getPagination();

    resp.getData().forEach(it -> {
       System.out.println(it.getReceipt().getId());
       System.out.println(it.getValue().getId());
    });

    // Buscar próxima página
    swp.getAllAccounts(
        new PaginationParams(String.valueOf(pagination.getCursor()), "3"), null
    ).getData()
     .forEach(it -> {
        System.out.println(it.getReceipt().getId());
        System.out.println(it.getValue().getId());
    });

```

Utilizamos um modelo de paginação baseada em *[cursor](https://slack.engineering/evolving-api-pagination-at-slack-1c1f644f8e12)*.
Os seguintes endpoints a suportam:

- Buscar todas as Contas (GET /accounts)
- Buscar todos os Ativos (GET /assets)
- Buscar todas as Transferências (GET /transfers)

Para utilizar a paginação, existem dois parâmetros opcionais que são passados via *[query parameters](https://branch.io/glossary/query-parameters/)* na URL da requisição:

- `limit`: Limite de itens para a resposta. Se omitido, seu valor padrão é igual a 100.
- `starting_after`: Utilizado para buscar os próximos itens em uma nova requisição a partir de um valor de `cursor`.

<aside class="notice">Todos os endpoints que devolvem uma lista são ordenados do mais novo ao mais antigo. A resposta é sempre uma <a href="#successresponse-lt-t-gt">SuccessResponse</a> que contém um campo do tipo <a href="#pagination">Pagination</a>.</aside>

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

```java
  OrgDTO org = swp.getOrganization().getData().getValue()
```


Busca informações sobre sua Organização.

`GET /organizations`

#### Retorno
* **API:** [SuccessResponse](#successresponse-lt-t-gt)<[Data](#data-lt-t-gt)<[Organization](#organization)>>
* **Node:** Promise<[SuccessResponse](#successresponse-lt-t-gt)<[Data](#data-lt-t-gt)<[Organization](#organization)>>>


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
```java 

    SuccessResponse<List<DataDTOReceipt<AccountDTO>>> resp =
    swp.getAllAccounts(
        new PaginationParams(null, "3"), null
    );

    PaginationResponse pagination = resp.getPagination();

    resp.getData().forEach(it -> {
       System.out.println(it.getReceipt().getId());
       System.out.println(it.getValue().getId());
    });

    // Buscar próxima página
    swp.getAllAccounts(
        new PaginationParams(String.valueOf(pagination.getCursor()), "3"), null
    ).getData()
     .forEach(it -> {
        System.out.println(it.getReceipt().getId());
        System.out.println(it.getValue().getId());
    });

```

Busca informações sobre todas as Contas já criadas pela sua Organização.

`GET /accounts?tag=<tag>&limit=<limit>&starting_after=<starting_after>`

#### Parâmetros de Query

Parâmetro | Descrição
--------- | -----------
tag | (opcional) [Tags](#tags-e-filtros) para filtragem
limit | (opcional) Limite de itens por página
starting_after | (opcional) ID do item a partir do qual a pagina deve começar

#### Retorno
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<Array<[Data](#data-lt-t-gt)<[Account](#account)>>>
* **Node:** Promise\<[SuccessResponse](#successresponse-lt-t-gt)\<Array<[Data](#data-lt-t-gt)<[Account](#account)>>>>


### 3. Conta específica

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

```java

   String ACC_ID = "3394abe6b49b3d718f572829adf2d7f186520d35be9e746e8d3d9366145b2fe1";
   
   AccountDTO acc = swp.getAccount(ACC_ID)
                       .getData()
                       .getValue();

```

Busca informações sobre uma Conta específica criada pela sua Organização.

`GET /accounts/:id`

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
id | ID da Conta

#### Retorno
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)<[Account](#account)>>
* **Node:** Promise\<[SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)<[Account](#account)>>>


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

```java

 SuccessResponse<List<DataDTOReceipt<AssetDTO>>> resp =
              swp.getAllAssets(
                new PaginationParams(null, "3"), null
            );

  PaginationResponse pagination = resp.getPagination();
  
  resp.getData().forEach(it -> {
     System.out.println(it.getValue().getId());
  });
  
  // Buscar próxima página
  swp.getAllAssets(
      new PaginationParams(String.valueOf(pagination.getCursor()), "3"), null
  ).getData()
   .forEach(it -> {
      System.out.println(it.getReceipt().getId());
      System.out.println(it.getValue().getId());
  });
        
```

Busca todos os Ativos emitidos pela sua Organização.

`GET /assets?tag=<tag>&limit=<limit>&starting_after=<starting_after>`

#### Parâmetros de Query

Parâmetro | Descrição
--------- | -----------
tag | (opcional) [Tags](#tags-e-filtros) para filtragem
limit | (opcional) Limite de itens por página
starting_after | (opcional) ID do item a partir do qual a pagina deve começar

#### Retorno
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<Array<[Data](#data-lt-t-gt)<[Asset](#asset)>>>
* **Node:** Promise\<[SuccessResponse](#successresponse-lt-t-gt)\<Array<[Data](#data-lt-t-gt)<[Asset](#asset)>>>>

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
```java 
  String ACC_ID = "3394abe6b49b3d718f572829adf2d7f186520d35be9e746e8d3d9366145b2fe1";
  
  //get first page
  SuccessResponse<List<DataDTOReceipt<TransferDTO>>> resp =
      swp.getAllTransfers(ACC_ID, new PaginationParams(null, "3"));

  PaginationResponse pagination = resp.getPagination();

  resp.getData().forEach(it -> System.out.println(it.getValue().getId()));

  // Buscar próxima página
  swp.getAllTransfers(ACC_ID, new PaginationParams(String.valueOf(pagination.getCursor()), "3"))
     .getData()
     .forEach(it -> {
          System.out.println(it.getReceipt().getId());
          System.out.println(it.getValue().getId());
     });
```

Busca todas as Transferências relacionadas a sua Organização ou Conta filha, incluindo transferências enviadas e recebidas.

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
* **Node:** Promise\<[SuccessResponse](#successresponse-lt-t-gt)\<Array\<[Data](#data-lt-t-gt)\<[Transfer](#transfer)>>>>

### 6. Lote de Transferências específico

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
```java
  String TRANSFER_ID = "3394abe6b49b3d718f572829adf2d7f186520d35be9e746e8d3d9366145b2fe1";
  TransferBatchDTO batchDTO = swp.getTransfer(TRANSFER_ID).getData().getValue();
  TransferDTO transfer = batchDTO.getActions().get(0);
```

Busca informações sobre um lote de Transferências relacionadas a sua Organização ou Conta filha.

`GET /transfers/:id`

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
id | ID do lote de Transferências

#### Retorno
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[TransferBatch](#transferbatch)>>
* **Node:** Promise\<[SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[TransferBatch](#transferbatch)>>>


## Realizar ações

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
  // Este campo é opcional. Por padrão, todas as Contas filhas suportam todos os Ativos da Organização e possuem saldo inicial zero.
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
```java

   String ASSET_ID = "07773f06becd47385d1e8d1e9bad3bd588ccd880fe746819257a6246e33551d3";

   String STARTING_BALANCE = "1.99";
  
   AccountDTO accDTO = swp.createAccount(
           new CreateAccountBuilder()
           .addStartingBalance(ASSET_ID, STARTING_BALANCE)
           .addTag("tag10")
           .build()
   ).getData()
   .getValue();
 
```

`POST /accounts`

#### Body
[NewAccount](#newaccount)

#### Retorno
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[Account](#account)>>
* **Node:** Promise\<[SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[Account](#account)>>>


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
```java
    String CODE = "TOKEN";
    String LIMIT = "100";
    List<String> TAGS = new ArrayList();
  
    AssetDTO assetDTO = swp.issueAsset(
            new NewAssetDTO(CODE, LIMIT, TAGS)
    ).getData().getValue();
```


`POST /assets`

#### Body
[NewAsset](#newasset)

#### Retorno
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[Asset](#asset)>>
* **Node:** Promise\<[SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[Asset](#asset)>>>

### 3. Realizar Transferências em lote

```shell
curl --request POST \
  -L https://api-sandbox.swipetech.io/transfers \
  -H 'accept: application/json' \
  -H 'accept-language: pt-BR' \
  -H 'content-type: application/json' \
  -H 'x-swp-api-key: <sua api key>' \
  -H 'x-swp-signature: <assinatura da requisição>' \
  -d '{"transfers":[{"from":"269de13d714b253b88fdf18620c3194078f7932d48855efc6e4d6dc57528c84c","to":"b0ea341bd255aa27eb38ef136aebfcaaffbc87103d872a4a218df7b434f5a6ad","amount":"121.22","asset":"b6039b3fb9c3e30945644cc394e6b1accb0a6c2844514aad0819a89d64b0184c"}],"memo":"01234567"}'
```

```javascript
import { memoHash, sha256 } from "@swp/swipe-sdk"

const memo = memoHash(sha256("1234567"))

// Note que isso é uma lista, mesmo que haja somente uma Transferência
swp.makeTransfers({
  actions: [
    {
      from: "44d351a02f2307153be74984a59675f2733ad5deb1fa9fb08b0a36fe3d15fd6d",
      to: "55c86a9027f2ff8c5d6ed1e2dbda01886b8b33f461341533d7391c14abe7aa40",
      asset: "07773f06becd47385d1e8d1e9bad3bd588ccd880fe746819257a6246e33551d3",
      amount: "1000",
    }
  ],
  memo
})
  .then(({data}) => {
    data.value.actions.forEach(tf => {
      console.log(tf.amount)
    })
  })
  .catch(error => {
    console.log(error)

    // exibir índice e código de erro das Transferências que falharam
    error.sub_errors
      .forEach((se, i) => {
        // é possível checar qual a Transferência que falhou através de seu campo `index`
        console.log(i, se.code, se.index)
      })
  })
```
```java

String FROM = "44d351a02f2307153be74984a59675f2733ad5deb1fa9fb08b0a36fe3d15fd6d";
String TO = "55c86a9027f2ff8c5d6ed1e2dbda01886b8b33f461341533d7391c14abe7aa40";
String ASSET = "07773f06becd47385d1e8d1e9bad3bd588ccd880fe746819257a6246e33551d3";
String AMOUNT = "1000";

swp.makeTransfers(
        new NewTransferBatchDTO(
                Arrays.asList(
                        new NewTransferDTO(FROM, TO, ASSET, AMOUNT)
                ),
                new Memo(MemoType.TEXT.name(), "1234")
        )
);

```



Executa uma lista de Transferências de forma atômica, isto é; se uma falhar, todas falharão.

Obs.: O campo opcional `memo` pode ser utilizado para [salvar informações na rede](#memo).

`POST /transfers`

#### Body
[TransferBatch](#transferbatch)

#### Retorno
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[TransferBatch](#transferbatch)>>
* **Node:** Promise\<[SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[TransferBatch](#transferbatch)>>>

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
```java
String ACC_ID = "44d351a02f2307153be74984a59675f2733ad5deb1fa9fb08b0a36fe3d15fd6d";
SuccessResponse<DataDTOReceipt<AccountDTO>> resp = swp.destroyAccount(ACC_ID);
```


Destrói uma Conta. Essa Conta deve ter saldo zero para todos seus Ativos.

`DELETE /accounts/:id`

<aside class="warning">Contas destruídas não podem ser recuperadas. Esta Ação não pode ser desfeita.</aside>

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
id | ID da Conta

#### Retorno
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[Account](#account)>>
* **Node:** Promise\<[SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[Account](#account)>>>

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
  memo: {
    type: "TEXT",
    value: "1234567"
  }
})



// Também é possível construir Ações usando funções auxiliares
import { createAccountAction, issueAssetAction, transferAction, memoText } from "@swp/swipe-sdk"

const memo = memoText("1234567")

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
  ],
  memo
})
```

```java
String ASSET = "07773f06becd47385d1e8d1e9bad3bd588ccd880fe746819257a6246e33551d3";

String ISSUE_ASSET_CODE = "TOKEN";
String ISSUE_ASSET_LIMIT = "100";
List<String> ISSUE_ASSET_TAGS = null;

String TRANSFER_FROM = "44d351a02f2307153be74984a59675f2733ad5deb1fa9fb08b0a36fe3d15fd6d";
String TRANSFER_TO = "55c86a9027f2ff8c5d6ed1e2dbda01886b8b33f461341533d7391c14abe7aa40";

String TRANSFER_AMOUNT = "1000";

String CREATE_ACC_STARTING_BALANCE = "10";

ActionBatchDTO result = swp.makeActionBatch(
        new ActionBatchBuilder()
                .addIssueAsset(ISSUE_ASSET_CODE, ISSUE_ASSET_LIMIT, ISSUE_ASSET_TAGS)
                .addTransfer(TRANSFER_FROM, TRANSFER_TO, ASSET, TRANSFER_AMOUNT)
                .addCreateAccount(
                        new CreateAccountBuilder()
                            .addStartingBalance(ASSET, CREATE_ACC_STARTING_BALANCE)
                            .build()
                )
                .build()
).getData()
 .getValue();
```



Executa um [lote de Ações](#lote-de-acoes)

Obs.: O campo opcional `memo` pode ser utilizado para [salvar informações na rede](#memo).

`POST /actions`

#### Body
[ActionBatch](#actionbatch)

#### Retorno
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[ActionBatch](#actionbatch)\>
* **Node:** Promise\<[SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[ActionBatch](#actionbatch)\>>

## Lote de Ações com Memo 

```javascript
import { createAccountAction, sha256 } from "@swp/swipe-sdk"

const memo1 = {
  type: "TEXT",
  value: "conteúdo do memo"
}

const memo2 = {
  type: "HASH",
  value: sha256("conteúdo do memo")
}

swp.makeActionBatch({
  actions: [
    createAccountAction(),
  ],
  memo: memo1 // -> tipo TEXT
  // memo: memo2 -> tipo HASH
})

// Também é possível utilizar funções utilitárias para criação do Memo
import { createAccountAction, memoText, memoHash, sha256 } from "@swp/swipe-sdk"

const memo1 = memoText("conteúdo do memo")
const memo2 = memoHash(sha256("conteúdo do memo"))

swp.makeActionBatch({
  actions: [
    createAccountAction(),
  ],
  memo: memo1 // -> tipo TEXT
  // memo: memo2 -> tipo HASH
})

// Utilize esta função para checar ou gerar o valor do hash.
const hash = sha256("conteúdo do memo")
```

```java
String ASSET = "07773f06becd47385d1e8d1e9bad3bd588ccd880fe746819257a6246e33551d3";

String TRANSFER_FROM = "44d351a02f2307153be74984a59675f2733ad5deb1fa9fb08b0a36fe3d15fd6d";
String TRANSFER_TO = "55c86a9027f2ff8c5d6ed1e2dbda01886b8b33f461341533d7391c14abe7aa40";

String TRANSFER_AMOUNT = "1000";

ActionBatchDTO result = swp.makeActionBatch(
        new ActionBatchBuilder()
                .addTransfer(TRANSFER_FROM, TRANSFER_TO, ASSET, TRANSFER_AMOUNT)
                .addMemo(new Memo(MemoType.TEXT.name(), "hello world!"))
                .build()
).getData()
 .getValue();
```

Todo [Lote de Ações](#lote-de-acoes) pode incluir um campo [Memo](#memovalue) com informações adicionais.

Existem dois tipos de Memo:

- TEXT: Qualquer texto codificado usando ASCII ou UTF-8, com até 28 bytes
- HASH: Um hash de 32 bytes. Normalmente utilizado para salvar informações confidenciais ou para informações que podem exceder o limite de tamanho to tipo `TEXT`.

Caso seja passado um valor inválido, a API retornará um [erro de validação](#tratamento-de-erros)

## Tags e filtros

Contas e Ativos podem conter uma ou mais `tags`, agregando informações para fins de organização dos dados.

### 1. Especificar tags na criação de Conta ou Ativo

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

```java
   
AccountDTO accDTO = swp.createAccount(
        new CreateAccountBuilder().addTag("fornecedor").build()
).getData().getValue();

String CODE = "TOKEN";
String LIMIT = "100";
AssetDTO assetDTO = swp.issueAsset(
        new NewAssetDTO(CODE, LIMIT, Arrays.asList("fornecedor"))
).getData().getValue();

```

As Ações de [criação de Conta](#realizar-acoes) e de [emissão de um Ativo](#realizar-acoes) possuem um parâmetro opcional `tags`. Ao ser especificado, é aplicada uma string `tags` às Contas ou Ativos criados, que pode ser usada para separá-los em categorias.

Cada Conta ou Ativo pode conter, no máximo, 10 tags.

Cada tag possui um limite de 2 a 200 caracteres. São válidos:

- `a-z` (diferenciam-se maiúsculas e minúsculas)
- `0-9`
- `-`
- `_`
- `\`
- `/`
- `|`
- `:`

### 2. Alterar tags

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
```java
   String ACC_ID = "44d351a02f2307153be74984a59675f2733ad5deb1fa9fb08b0a36fe3d15fd6d";
   TagsDTO tags = swp.updateTags(ACC_ID, Arrays.asList("cliente")).getData().getValue();
```

`PUT /tags/:id`

Remove as tags de uma Conta ou Ativo e as substitui por um novo conjunto.

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
id | ID da Conta ou Ativo

#### Body
[NewTags](#newtags)

#### Retorno
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[Tags](#tags)>>
* **Node:** Promise\<[SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[Tags](#tags)>>>

### 3. Filtrar Contas por tag

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
```java
  List<DataDTOReceipt<AccountDTO>> resp = 
      swp.getAllAccounts(null, new FilterDTO("fornecedor")).getData();
```

Filtra Contas que contêm uma tag específica.

`GET /accounts?tag=<tag>`

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
tag | Tag para filtragem

#### Retorno
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<Array\<[Data](#data-lt-t-gt)\<[Account](#account)>>>
* **Node:** Promise\<[SuccessResponse](#successresponse-lt-t-gt)\<Array\<[Data](#data-lt-t-gt)\<[Account](#account)>>>>

### 4. Filtrar Ativos por tag

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

```java
     List<DataDTOReceipt<AssetDTO>> resp = 
          swp.getAllAssets(null, new FilterDTO("fornecedor")).getData();
```

Filtra Ativos que contêm uma tag específica.

`GET /assets?tag=<tag>`

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
tag | Tag para filtragem

#### Retorno
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<Array\<[Data](#data-lt-t-gt)\<[Asset](#asset)>>>
* **Node:** Promise\<[SuccessResponse](#successresponse-lt-t-gt)\<Array\<[Data](#data-lt-t-gt)\<[Asset](#asset)>>>>

## Outros

### 1. Resetar Organização (disponível apenas em ambiente sandbox)

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
    console.log("Feito!")
  )
  .catch(error =>
    console.log(error)
  )
```

```java
  swp.resetOrganization();
```

Esta função retorna a Organização a seu estado inicial, ou seja:

* Todas as Contas filhas da Organização serão removidas e seus saldos devolvidos à Organização.
* Os IDs da Organização e Ativos serão alterados.

`DELETE /organizations`

<aside class="warning">Essa Ação é destrutiva e não pode ser desfeita.</aside>

### 2. Revoke de um par de credenciais

Revoga um par de credenciais, tornando-as inválidas e impossibilitando o acesso à aplicação.

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
```java
     ResponseToken respToken = swp.getToken().getData().getValue();
     swp.revokeCredentials(respToken.getToken()).getData();
```

Esta função ocorre em 2 passos. Primeiro, busca-se um token de Revoke (válido por 5 minutos):

`GET /organizations/revoke`

#### Retorno
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[ResponseToken](#responsetoken)>>
* **Node:** Promise\<[SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[ResponseToken](#responsetoken)>>>

Em seguida, utiliza-se o token obtido no passo anterior para executar o Revoke:

`POST /organizations/revoke/:token`

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
token | token de Revoke

#### Retorno
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<null>>
* **Node:** Promise\<[SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<null>>>

<aside class="warning">Essa Ação é destrutiva e não pode ser desfeita.</aside>
