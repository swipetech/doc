# Integration

There is an official [Node.js](https://github.com/Swipetech/swp-node-sdk) SDK which we keep up-to-date. We also develop SDKs for other languages according to our customers' needs.

We strongly recommend using an SDK, because they do away with a great deal of complexity involved in integration, such as authentication, error handling and support to new versions.

Since our API is REST-based, it is also possible to use it directly.

While reading this section, use the tab on the right to follow integration examples using SDKs or via our REST API.

## SDK Integration

Select the **javascript** tab on the right to view basic examples of how to use the SDK.

### Installation

```javascript
// ES2015 or TypeScript
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

<aside class="warning"><b>Caution:</b> integration must always be performed using a Node.js server, never from a web browser. Starting the Javascript SDK from a browser may expose your <b>API Key</b> and <b>Secret</b>, even while using code obfuscation practices.</aside>

### Start-up

```javascript
// Starts on the Production environment
const swp = Swipe.init({
  apiKey: "your api key",
  secret: "your secret key",
  language: Swipe.languages.EN_US,
})
```

```javascript
// Starts on the Sandbox environment
const swp = Swipe.init({
  apiKey: "your api key",
  secret: "your secret key",
  sandbox: true,
  language: Swipe.languages.EN_US,
})
```

After installation, the first step is to start up the SDK with a valid `API Key`, `Secret` and `Language`.

For testing purposes, we provide a Sandbox environment. See an example on the right tab.

## REST API Integration

Select the **shell** tab on the right to view basic examples of integration via API using `curl`.

<aside class="warning">Remember to include <code>https</code> in all requests. In case the API receives an insecure connection, an <code>insecure_connection</code> error will be returned.</aside>

### Environments

Use the following domains for your requests.

- **Production:** `https://0-8.api.swipetech.io`
- **Sandbox:** `https://api-sandbox.swipetech.io`

### Authentication

Note that it is only necessary to do anything regarding request authentication if you choose to integrate directly to our API.
Integration via SDK streamlines that process completely.

#### Headers

```shell
curl -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <API_KEY>" \
  -H "X-Swp-Signature: <SIGNATURE>" \
  https://api.swipetech.io/organizations
```

We use a model based on an `API Key` and a `Secret` to authenticate requests.
All requests must include the following headers:

- `X-Swp-Timestamp`: Number of seconds since [Unix Epoch](https://en.wikipedia.org/wiki/Unix_time).
A 5-minute margin is allowed regarding the time the API receives the request.
- `X-Swp-Api-Key`: API Key as a string
- `X-Swp-Signature`: Signature of the request (explained below)

#### Signature (X-Swp-Signature)

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

To generate a request signature, follow the guide below while viewing the **javascript** tab.

- Concatenate: `method` + `timestamp` + `request path` + `body string`
- Use your `secret` to create a HMAC-SHA-256 from the string obtained above
- Finally, convert the result to Base64

Example of a signature:

Field | Value
----  | -----
**Path** | /accounts
**Method** | GET
**Body** |
**Secret** | 71ad81f98fbbab22c9d74948d2899a65027208197291d11e2065c3a9c62fe1f0
**Timestamp** | 1540920260
**Signature** | /h02U+jDJWOG1npOvL1pH79hgGWT7mWymzxISP5IphA=

### Language

```shell
curl -H "Content-Type: application/json" \
  -H "Accept-Language: en-US" \
  -H "X-Swp-Api-Key: <your api key>" \
  -H "X-Swp-Signature: <request signature>" \
  https://api.swipetech.io/organizations
```

To set the language for API responses, use the following header on requests:

- `Accept-Language`: [Selected language](#supported-languages) (`pt-BR` by default)

## Formatting Asset values

There are some formatting rules for asset values that have to be observed.

- Values must always be in `string` format.

- The max value issued for an Asset or sent through a Transfer is 920_000_000_000 (920 billion).

- The minimum value issued for an Asset or sent through a Transfer is 0.0000001. Thus, the max precision is of 7 decimal places.

<aside class="warning">Some programming languages (such as JavaScript) have problems with maintaining precision on a number amount. It is recommended to use <b>Big Number</b> libraries that can record arbitrary precision decimal numbers without a loss of precision.</aside>

## Querying information

This section lists the endpoints used to query details on an [Organization](#organizations), [Accounts](#accounts) or [Assets](#assets).

### Pagination for GET requests

```shell
curl -X GET \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <your api key>" \
  -H "X-Swp-Signature: <request signature>" \
  https://api.swipetech.io/accounts?limit=10&starting_after=10003
```

```javascript
swp.getAllAccounts({limit: "10"})
  .then(({data, pagination}) => {
    data.forEach(({ receipt, value }) => {
      console.log(receipt.id)
      console.log(value.id)
    })

    // load the second page
    return swp.getAllAccounts({ limit: "10", starting_after: pagination.cursor})
  })
  .then(({data}) =>
    data.forEach(({ receipt, value }) => {
      console.log(receipt.id)
      console.log(value.id)
    })
  )
```

We use a [cursor](https://slack.engineering/evolving-api-pagination-at-slack-1c1f644f8e12)-based pagination model.
The following endpoints support it:

- Get all Accounts (GET /accounts)
- Get all Assets (GET /assets)
- Get all Transfers (GET /transfers)

To use pagination, there are two optional parameters that are sent via [query parameters](https://branch.io/glossary/query-parameters) on the request URL:

- `limit`: The limit of items for the response. If unused, the default value is 100.
- `starting_after`: Used for querying the next items in a new request from a `cursor` value.

<aside class="notice">All endpoints that return a list are sorted from latest to oldest. The response is always a <a href="#successresponse-lt-t-gt">SuccessResponse</a> containing a <a href="#pagination">Pagination</a>-type field.</aside>

### 1. Organization

```shell
curl -X GET \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <your api key>" \
  -H "X-Swp-Signature: <request signature>" \
  https://api.swipetech.io/organizations
```

```javascript
swp.getOrganization()
  .then(({data}) => console.log(data.value.name))
  .catch(error =>
    console.log(error)
  )
```

Query information on your Organization.

`GET /organizations`

#### Return
* **API:** [SuccessResponse](#successresponse-lt-t-gt)<[Data](#data-lt-t-gt)<[Organization](#organization)>>
* **Node:** Promise\<[SuccessResponse](#successresponse-lt-t-gt)<[Data](#data-lt-t-gt)<[Organization](#organization)>>>


### 2. All Accounts

```shell
curl -X GET \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <your api key>" \
  -H "X-Swp-Signature: <request signature>" \
  https://api.swipetech.io/accounts?limit=10
```

```javascript
swp.getAllAccounts({limit: "10"})
  .then(({data, pagination}) => {
    data.forEach(({ receipt, value }) => {
      console.log(receipt.id)
      console.log(value.id)
    })

    // loading the second page
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

Query information on all Accounts ever created by your Organization.

`GET /accounts?tag=<tag>&limit=<limit>&starting_after=<starting_after>`

#### Query parameters

Parameter | Description
--------- | -----------
tag | (optional) [Tags](#tags-and-filters) for filtering
limit | (optional) Limit of items per page
starting_after | (optional) Item ID from which the page should begin

#### Return
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<Array<[Data](#data-lt-t-gt)<[Account](#account)>>>
* **Node:** Promise\<[SuccessResponse](#successresponse-lt-t-gt)\<Array<[Data](#data-lt-t-gt)<[Account](#account)>>>>


### 3. A specific Account

```shell
curl -X GET \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <your api key>" \
  -H "X-Swp-Signature: <request signature>" \
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

Query information on a specific Account created by your Organization.

`GET /accounts/:id`

#### URL parameters

Parameter | Description
--------- | -----------
id | Account ID

#### Return
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)<[Account](#account)>>
* **Node:** Promise\<[SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)<[Account](#account)>>>


### 4. All Assets

```shell
curl -X GET \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <your api key>" \
  -H "X-Swp-Signature: <request signature>" \
  https://api.swipetech.io/assets?limit=10
```

```javascript
swp.getAllAssets({limit: "10"})
  .then(({ data, pagination }) => {
    data.forEach(({ receipt, value }) => {
      console.log(receipt.id)
      console.log(value.code)
    })

    // loading the second page
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

Query all Assets issued by your Organization.

`GET /assets?tag=<tag>&limit=<limit>&starting_after=<starting_after>`

#### Query parameters

Parameters | Description
--------- | -----------
tag | (optional) [Tags](#tags-and-filters) for filtering
limit | (optional) Limit of items per page
starting_after | (optional) Item ID from which the page should begin

#### Return
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<Array<[Data](#data-lt-t-gt)<[Asset](#asset)>>>
* **Node:** Promise\<[SuccessResponse](#successresponse-lt-t-gt)\<Array<[Data](#data-lt-t-gt)<[Asset](#asset)>>>>

### 5. All Transfers relative to an Account

```shell
curl -X GET \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <your api key>" \
  -H "X-Swp-Signature: <request signature>" \
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

    // loading the second page
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

Query all Transfers related to your Organization or child Account, including both sent and received Transfers.

`GET /accounts/:id/transfers?limit=<limit>&starting_after=<starting_after>`

#### URL parameters

Parameter | Description
--------- | -----------
id | ID da Conta ou da Organização

#### Query parameters

Parameter | Description
--------- | -----------
limit | (optional) Limit of items per page
starting_after | (optional) Item ID from which the page should begin

#### Return
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<Array\<[Data](#data-lt-t-gt)\<[Transfer](#transfer)>>>
* **Node:** Promise\<[SuccessResponse](#successresponse-lt-t-gt)\<Array\<[Data](#data-lt-t-gt)\<[Transfer](#transfer)>>>>

### 6. A specific Transfer Batch

```shell
curl -X GET \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <your api key>" \
  -H "X-Swp-Signature: <request signature>" \
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

Query information on a Transfer batch related to your Organization or child Account.

`GET /transfers/:id`

#### URL parameters

Parameter | Description
--------- | -----------
id | Transfer batch ID

#### Return
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[TransferBatch](#transferbatch)>>
* **Node:** Promise\<[SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[TransferBatch](#transferbatch)>>>


## Perform Actions

### 1. Create a new Account

```shell
curl -X POST \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <your api key>" \
  -H "X-Swp-Signature: <request signature>" \
  https://api.swipetech.io/accounts
```

```javascript
swp.createAccount({
  // This field is optional. By default, all children Accounts support all of the Organization's Assets and have a starting balance of zero.
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

#### Return
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[Account](#account)>>
* **Node:** Promise\<[SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[Account](#account)>>>


### 2. Issue an Asset

```shell
curl -X POST \
  https://api.swipetech.io/assets \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <your api key>" \
  -H "X-Swp-Signature: <request signature>" \
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

#### Return
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[Asset](#asset)>>
* **Node:** Promise\<[SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[Asset](#asset)>>>

### 3. Make batch Transfers

```shell
curl --request POST \
  -L https://api-sandbox.swipetech.io/transfers \
  -H 'accept: application/json' \
  -H 'accept-language: en-US' \
  -H 'content-type: application/json' \
  -H 'x-swp-api-key: <your api key>' \
  -H 'x-swp-signature: <request signature>' \
  -d '{"transfers":[{"from":"269de13d714b253b88fdf18620c3194078f7932d48855efc6e4d6dc57528c84c","to":"b0ea341bd255aa27eb38ef136aebfcaaffbc87103d872a4a218df7b434f5a6ad","amount":"121.22","asset":"b6039b3fb9c3e30945644cc394e6b1accb0a6c2844514aad0819a89d64b0184c"}],"memo":"01234567"}'
```

```javascript
// Note that this is a list, even though there may be only one Transfer
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

    // show index and error code of failed Transfers
    error.sub_errors
      .forEach((se, i) => {
        // it is possible to check which Transfer failed by its `index` field
        console.log(i, se.code, se.index)
      })
  })
```

Performs a list of Transfers atomically, which is to say, if one fails, all will fail.

Obs.: The optional `memo` field can be used for saving information on the [network](#dlt-networks).

`POST /transfers`

#### Body
[TransferBatch](#transferbatch)

#### Return
* **API:** [SuccessResponse](#response-lt-t-gt)\<[Data](#data-lt-t-gt)\<[TransferBatch](#transferbatch)>>
* **Node:** Promise\<[SuccessResponse](#response-lt-t-gt)\<[Data](#data-lt-t-gt)\<[TransferBatch](#transferbatch)>>>

### 4. Destroy an Account

```shell
curl -X DELETE \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <your api key>" \
  -H "X-Swp-Signature: <request signature>" \
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

Destroys an Account. That Account must hold a balance of zero for all its Assets.

`DELETE /accounts/:id`

<aside class="warning">Destroyed Accounts cannot be recovered. This Action cannot be undone.</aside>

#### URL parameters

Parameter | Description
--------- | -----------
id | Account ID

#### Return
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[Account](#account)>>
* **Node:** Promise\<[SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[Account](#account)>>>

### 5. Action Batch

```shell
curl -X POST \
  https://api.swipetech.io/actions \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <your api key>" \
  -H "X-Swp-Signature: <request signature>" \
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


// It is also possible to build Actions using auxiliary functions
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

Performs an [Action Batch](#action-batch)

Obs.: The optional `memo` field can be used for saving information on the [network](#dlt-networks).

`POST /actions`

#### Body
[ActionBatch](#actionbatch)

#### Return
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[ActionBatch](#actionbatch)\>
* **Node:** Promise\<[SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[ActionBatch](#actionbatch)\>>

## Tags and filters

Accounts and Assets can have one or more `tags`, adding information for data organization purposes.

### 1. Specifying tags when creating Accounts or Assets

```shell
curl -X POST \
  -L https://api.swipetech.io/accounts \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <your api key>" \
  -H "X-Swp-Signature: <request signature>" \
  -d '{"tags":["supplier"]}'

curl -X POST \
  -L https://api.swipetech.io/assets \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <your api key>" \
  -H "X-Swp-Signature: <request signature>" \
  -d '{"code": "TOKEN", "tags":["supplier"]}'
```

```javascript
swp.createAccount({tags: ["supplier"]})
  .then(({data}) =>
    console.log(data.value.tags)
  )
  .catch(error =>
    console.log(error)
  )

swp.issueAsset({
  code: "TOKEN",
  tags: ["supplier"]
})
  .then(({data}) =>
    console.log(data.value.tags)
  )
  .catch(error =>
    console.log(error)
  )
```

The Actions for [creating Accounts](#perform-actions) and [issuing Assets](#perform-actions) have an optional parameter called `tags`. If specified, a `tags` string is applied to the created Accounts or Assets, which can be used to separate them into categories.

Each Account or Asset can hold a max of 10 tags.

Each tag has a limit ranging from 2 to 200 characters. Valid characters:

- `a-z` (case sensitive)
- `0-9`
- `-`
- `_`
- `\`
- `/`
- `|`
- `:`

### 2. Change tags

```shell
curl -X PUT \
  -L https://api.swipetech.io/tags/44d351a02f2307153be74984a59675f2733ad5deb1fa9fb08b0a36fe3d15fd6d \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <your api key>" \
  -H "X-Swp-Signature: <request signature>" \
  -d '{"tags":["customer"]}'
```

```javascript
const id = '44d351a02f2307153be74984a59675f2733ad5deb1fa9fb08b0a36fe3d15fd6d'

swp.updateTags(id, ["customer"])
  .then(({data}) =>
    console.log(data.value.tags)
  )
  .catch(error =>
    console.log(error)
  )
```

`PUT /tags/:id`

Removes tags from an Account or Asset an replaces them by a new set of tags.

#### URL parameters

Parameter | Description
--------- | -----------
id | Account ID or Asset ID

#### Body
[NewTags](#newtags)

#### Return
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[Tags](#tags)>>
* **Node:** Promise\<[SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[Tags](#tags)>>>

### 3. Filter Accounts by tag

```shell
# Filtering Accounts by tag
curl -X GET \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <your api key>" \
  -H "X-Swp-Signature: <request signature>" \
  https://api.swipetech.io/accounts?tag=supplier
```

```javascript
// Filtering Accounts by tag
swp.getAllAccounts({ tag: "supplier" })
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

Filters Accounts that have a specific tag.

`GET /accounts?tag=<tag>`

#### URL parameters

Parameter | Description
--------- | -----------
tag | Tag to be filtered

#### Return
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<Array\<[Data](#data-lt-t-gt)\<[Account](#account)>>>
* **Node:** Promise\<Array\<[SuccessResponse](#successresponse-lt-t-gt)\<Array\<[Data](#data-lt-t-gt)\<[Account](#account)>>>>

### 4. Filter Assets by tag

```shell
curl -X GET \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <your api key>" \
  -H "X-Swp-Signature: <request signature>" \
  https://api.swipetech.io/assets?tag=supplier
```

```javascript
swp.getAllAssets({ tag: "crypto" })
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

Filters Assets that have a specific tag.

`GET /assets?tag=<tag>`

#### URL parameters

Parameter | Description
--------- | -----------
tag | Tag to be filtered

#### Return
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<Array\<[Data](#data-lt-t-gt)\<[Asset](#asset)>>>
* **Node:** Promise\<[SuccessResponse](#successresponse-lt-t-gt)\<Array\<[Data](#data-lt-t-gt)\<[Asset](#asset)>>>>

## Others

### 1. Reset Organization (available only on the sandbox environment)

```shell
curl -X DELETE \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <your api key>" \
  -H "X-Swp-Signature: <request signature>" \
  https://api.swipetech.io/organizations
```

```javascript
swp.resetOrganization()
  .then(() =>
    console.log("Done!")
  )
  .catch(error =>
    console.log(error)
  )
```
This function returns the Organization to its initial state, which means:

* All children Accounts of the Organization will be removed and their balances returned to the Organization.
* The Organization ID and Asset IDs will be changed.

`DELETE /organizations`

<aside class="warning">This is a destructive Action and cannot be undone.</aside>

### 2. Revoke a credential pair

Revokes a credential pair, making them invalid and disabling access to the application.

```shell
# Searches for the revocation token
curl -X GET \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <your api key>" \
  -H "X-Swp-Signature: <request signature>" \
  https://api.swipetech.io/organizations/revoke

# Performs the revoking  
curl -X POST \
  -H "Content-Type: application/json" \
  -H "X-Swp-Api-Key: <your api key>" \
  -H "X-Swp-Signature: <request signature>" \
  https://api.swipetech.io/organizations/revoke/eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJUeXBlIjoiUkVWT0tFIENSRURFTlRJQUxTIiwiZXhwIjoxNTUwODYyNjY2LCJUaW1lc3RhbXAiOjE1NTA4NjIzNjZ9.s9UbrJmWQXVpIeXAb9gjWwRe19iWV1gYIaoxXOQ0_1A
```

```javascript
swp.getToken()
  .then(({data}) => {
      console.log(data.value.token)
      // eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJUeXBlIjoiUkVWT0tFIENSRURFTlRJQUxTIiwiZXhwIjoxNTUwODYyNjY2LCJUaW1lc3RhbXAiOjE1NTA4NjIzNjZ9.s9UbrJmWQXVpIeXAb9gjWwRe19iWV1gYIaoxXOQ0_1A

      return swp.revokeCredentials(data.value.token)
  })
  .then(() => console.log("Revoking successful"))
  .catch(error =>
    console.log(error)
  )
```

This function happens in 2 steps. First, a revocation token (valid for 5 minutes) is searched:

`GET /organizations/revoke`

#### Return
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[ResponseToken](#responsetoken)>>
* **Node:** Promise\<[SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<[ResponseToken](#responsetoken)>>>

Then, the obtained token is used to act out the revoking:

`POST /organizations/revoke/:token`

#### URL parameters

Parameter | Description
--------- | -----------
token | Revocation token

#### Return
* **API:** [SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<null>>
* **Node:** Promise\<[SuccessResponse](#successresponse-lt-t-gt)\<[Data](#data-lt-t-gt)\<null>>>

<aside class="warning">This is a destructive Action and cannot be undone.</aside>
