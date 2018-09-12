# Integração

Existem algumas maneiras diferentes de integrar à nossa solução. Mantemos SDKs oficiais nas seguintes linguagens:

- [Golang](https://github.com/Swipetech/swp-go-sdk)
- [Node.js](https://github.com/Swipetech/swp-node-sdk)

Além disso, nossa API é baseada em REST, por isso é completamente possível utilizá-la diretamente. 

A cada seção desta documentação, é possível visualizar exemplos à direita para cada modo de integração.

<aside class="notice">Temos um limite de 5 requests/segundo por IP (Caso necessite exceder esse limite, entre em contato conosco). Se ultrapassar isso, você receberá um erro com código <code>too_many_requests</code>.</aside>

## REST API

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

Utilizamos um modelo de `Api Key` e `Secret` para autenticar as requisições.
Todas devem incluir ao menos dois headers:

- `X-Swp-Api-Key`: Api Key como string
- `X-Swp-Signature`: Assinatura do request (Como explicado logo abaixo)

#### Assinatura (X-Swp-Signature)

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

Para configurar o idioma de resposta da API, utilize o seguinte header nas requisições:

- `Accept-Language`: [Idioma desejado](#idiomas-suportadas)

## SDKs

### Instalação Golang

Para baixar a versão mais recente:

`go get github.com/swipetech/swp-go-sdk`

Para baixar uma versão específica

`go get github.com/swipetech/swp-go-sdk@<version>`

<aside class="notice">Lembre-se de substituir <code>&lt;version&gt;</code> pela versão desejada (e.g. <code>0.5.0</code>)</aside>

### Instalação Node.js

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
Busca informações sobre sua Organização.

`GET /organizations`

#### Retorno
[OrganizationResponse](#organizationresponse)


### 2. Todas as Contas
Busca informações sobre todas as Contas já criadas pela sua Organização.

`GET /accounts`

#### Retorno
[AccountsResponse](#accountsresponse)


### 3. Uma Conta específica
Busca informações sobre uma Conta específica criada pela sua Organização.

`GET /accounts/:id`

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
id | ID da Conta 

#### Retorno
[AccountResponse](#accountresponse)


### 4. Todos os Ativos
Busca todos os Ativos criados pela sua Organização.

`GET /assets`

#### Retorno
[AssetsResponse](#assetsresponse)

### 5. Informações sobre um Pagamento específico
Busca informações sobre um Pagamento relacionado à sua Organização ou Contas filhas. 

`GET /payments/:id`

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
id | ID do Pagamento 

#### Retorno
[PaymentResponse](#paymentresponse)


### 6. Histórico de Pagamentos
Busca todos os Pagamentos segundo alguns filtros especificados. 

`GET /payments?from=<fromID>&to=<toID>&asset=<assetID>`

#### Parâmetros de Query

Parâmetro | Descrição
--------- | ---------
from | ID do remetente (Opcional)
to | ID do destinatário (Opcional)
asset | ID do Ativo (Opcional)

#### Retorno
[PaymentsResponse](#paymentsresponse)


## Monitorar Ações em tempo real

É possível monitorar algumas [Ações](#acao) em tempo real. 
Isso é possível através de [Server-sent events](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events).
Por esse motivo, é necessário incluir no request um header `Accept` com o valor `text/event-stream`.


### 1. Monitorar Pagamentos em tempo real
Escuta Pagamentos em tempo real para uma Conta filha ou para sua Organização. 
Aceita um ID de uma Conta filha ou da sua Organização.  

PS: Apenas Pagamentos feitos a partir da chamada do endpoint serão notificados.

`GET /accounts/:id/payments`

#### Parâmetros de URL
Nome | Valor
---- | -----
id | ID da Conta ou da sua Organização

#### Headers
Nome | Valor
---- | -----
Accept | text/event-stream

#### Retorno
A cada Pagamento, um evento do tipo `payment` será emitido contendo uma instância de [PaymentReceipt](#paymentreceipt) 


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
