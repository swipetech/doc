# API REST

<aside class="warning">Lembre-se de incluir <code>https</code> em todas suas requisições. Em caso de uma conexão não segura, a API retornará um erro `APP-9`.</aside>

## Ambiente de Produção

Para utilizar o ambiente de Produção, utilize o seguinte domínio para suas requisições:

`https://api.swipetech.io`

## Ambiente de Sandbox

Para utilizar o ambiente de Sandbox, utilize o seguinte domínio para suas requisições:
 
`https://api.sandbox.swipetech.io`

## Autenticação

Só é necessário se preocupar com a autenticação das chamadas caso opte por integrar diretamente à nossa API. 
Nossos SDKs abstraem completamente essa complexidade.

### Headers

Utilizamos um modelo de `Api Key` e `Secret` para autenticar as requisições.
Todas devem incluir ao menos dois headers:

- `X-Swp-Api-Key`: Api Key como string
- `X-Swp-Signature`: Assinatura do request (Como explicado logo abaixo)

### Assinatura (X-Swp-Signature)

Para gerar uma assinatura do request, siga esses passos:

- Concatene o `path` completo com a string do `body` (JSON) do request.
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

## Língua 

Para configurar a língua de resposta API com, utilize o seguinte header:

- `Accept-Language`: Língua desejada (e.g. `pt-BR`, `en-US`)

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


### 5. Todos os Pagamentos para sua Organização
Busca todos os Pagamentos em que o destinatário é sua Organização.

`GET /payments`

#### Retorno
[PaymentsResponse](#paymentsresponse)


### 6. Todos os Pagamentos para uma Conta
Busca todos os Pagamentos em que o destinatário é uma de suas Contas filhas.

`GET /accounts/:id/payments`

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
id | ID da Conta

#### Retorno
[PaymentsResponse](#paymentsresponse)


### 7. Informações sobre um Pagamento específico
Busca informações sobre um Pagamento relacionado à sua Organização ou Contas filhas. 

`GET /payments/:id`

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
id | ID do Pagamento 

#### Retorno
[PaymentResponse](#paymentresponse)


## Monitorar em tempo real

Alguns endpoints possuem suporte a [Server-sent events](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events).
Isto permite o monitoramento em tempo real de um recurso específico. 
Para isso, basta incluir na chamada um header `Accept` com o valor `text/event-stream`. Segue a lista de endpoints que suportam essa funcionalidade:


### 1. Monitorar Pagamentos para sua Organização em tempo real
Escuta Pagamentos para sua Organização em tempo real que forem realizados a partir da chamada do endpoint. 
Os eventos possuem o nome de `payment`.

`GET /payments`

#### Headers
Nome | Valor
---- | -----
Accept | text/event-stream

#### Retorno
A cada Pagamento, um evento do tipo `payment` será emitido contendo uma instância de [PaymentReceipt](#paymentreceipt)


### 2. Monitorar Pagamentos para uma Conta em tempo real
Escuta Pagamentos para uma Conta filha em tempo real que forem realizados a partir da chamada do endpoint.

`GET /accounts/:id/payments`

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

