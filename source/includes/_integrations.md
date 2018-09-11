# Formas de integrar

Existem algumas maneiras diferentes de integrar à nossa solução. Mantemos SDKs oficiais nas seguintes linguagens:

- [Golang](https://github.com/Swipetech/swp-go-sdk)
- [Node.js](https://github.com/Swipetech/swp-node-sdk)

Além disso, nossa API é baseada em REST, por isso é completamente possível utilizá-la diretamente. 

A cada seção desta documentação, é possível visualizar exemplos à direita para cada modo de integração.

## SDKs

### Instalação Golang

Para baixar a versão mais recente:

`go get github.com/swipetech/swp-go-sdk`

Para baixar uma versão específica

`go get github.com/swipetech/swp-go-sdk@<version>`

<aside class="notice">Lembre-se de substituir <code>&lt;version&gt;</code> pela versão desejada (e.g. <code>0.5.0</code>.)</aside>

### Instalação Node.js

Via npm:

`npm i @swp/wallet-sdk`

Via yarn:

`yarn add @swp/wallet-sdk`

<aside class="warning">A integração deve ser realizada sempre a partir de um servidor Node.js, nunca a partir de um navegador. Não é seguro armazenar seu <b>api key</b> e <b>secret</b> em um código JavaScript executado no navegador, mesmo que sejam utilizadas práticas de <b>code obfuscation</b>.</aside>

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

O primeiro passo, após a [instalação](#sdks), é inicializar o SDK com uma `Api Key`, um `Secret` e uma `Língua` válidos.

Para fins de testes, recomendamos que utilize nosso ambiente de Sandbox.

## API REST

<aside class="warning">Lembre-se de incluir <code>https</code> em todas suas requisições. Em caso de uma conexão não segura, a API retornará um erro `APP-9`.</aside>

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

Utilizamos um modelo de `Api Key` e `Secret` para autenticar as requisições.
Todas devem incluir ao menos dois headers:

- `X-Swp-Api-Key`: Api Key como string
- `X-Swp-Signature`: Assinatura do request (Como explicado logo abaixo)

#### Assinatura (X-Swp-Signature)

Para gerar uma assinatura do request, siga esses passos:

- Concatene o `path` com a string do `body` (JSON) do request.
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

#### Outros 

Também é possível configurar a língua da API com o seguinte header:

- `Accept-Language`: Língua desejada (e.g. `pt-BR`, `en-US`)
