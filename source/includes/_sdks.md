# SDKs

## Instalação Golang

Para baixar a versão mais recente:

`go get github.com/swipetech/swp-go-sdk`

Para baixar uma versão específica

`go get github.com/swipetech/swp-go-sdk@<version>`

<aside class="notice">Lembre-se de substituir <code>&lt;version&gt;</code> pela versão desejada (e.g. <code>0.5.0</code>)</aside>

## Instalação Node.js

Via npm:

`npm i @swp/swipe-sdk`

Via yarn:

`yarn add @swp/swipe-sdk`

<aside class="warning"><b>Atenção:</b> a integração deve ser realizada sempre a partir de um servidor Node.js, nunca a partir de um navegador. Inicializar o SDK JavaScript a partir de um navegador poderá expor indevidamente seu <b>Api Key</b> e <b>Secret</b>, mesmo que sejam utilizadas práticas de <i>code obfuscation</i>.</aside>


## Inicialização

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

## Buscar informações

Utilize os métodos abaixo para buscar mais detalhes sobre sua Organização, suas Contas ou Ativos.

### Organização

Busca informações sobre sua Organização.

```go
organization, err := swp.GetOrganization()
```

```javascript
swp.getOrganization()
  .then(res => console.log(res.organization))
```

## Monitorar em tempo real

**TODO**

## Executar ações

**TODO**
