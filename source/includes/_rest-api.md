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

### Outros 

Também é possível configurar a língua da API com o seguinte header:

- `Accept-Language`: Língua desejada (e.g. `pt-BR`, `en-US`)

Todos os endpoints seguem um padrão de resposta único. Um campo obrigatório `data` e um `error` opcional.
Para evitar repetição, pode-se assumir que todos os retornos descritos abaixo são contidos dentro do campo `data`. 
Para entender como lidar com erros, leia a seção sobre [tratamento de erros](#tratamento-de-erros) 

## Buscar informações

Utilize os endpoints abaixo para buscar mais detalhes sobre sua Organização, suas Contas ou Ativos.

### Organização
Busca informações sobre sua Organização.

`GET /organizations`

#### Retorno
[OrganizationResponse](#organization-response)


### Todas as Contas
Busca informações sobre todas as Contas já criadas pela sua Organização.

`GET /accounts`

#### Retorno
[AccountsResponse](#accounts-response)


### Uma Conta específica
Busca informações sobre uma Conta específica criada pela sua Organização.

`GET /accounts/:id`

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
id | ID da Conta 

#### Retorno
[AccountResponse](#account-response)


### Todos os Ativos
Busca todos os Ativos criados pela sua Organização.

`GET /assets`

#### Retorno
[AssetsResponse](#assets-response)


### Todos os Pagamentos para sua Organização
Busca todos os Pagamentos em que o destinatário é sua Organização.

`GET /payments`

#### Retorno
[PaymentsResponse](#payments-response)


### Todos os Pagamentos para uma Conta
Busca todos os Pagamentos em que o destinatário é uma de suas Contas filhas.

`GET /accounts/:id/payments`

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
id | ID da Conta

#### Retorno
[PaymentsResponse](#payments-response)


### Informações sobre um Pagamento específico
Busca informações sobre um Pagamento relacionado à sua Organização ou Contas filhas. 

`GET /payments/:id`

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
id | ID do Pagamento 

#### Retorno
[PaymentResponse](#payment-response)


## Suporte a streaming

Alguns endpoints possuem suporte a [Server-sent events](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events). Isto permite monitoramento em tempo real de um recurso específico. 
Para isso, basta incluir na chamada um header `Accept` com o valor `text/event-stream`. Segue a lista de endpoints que suportam essa funcionalidade:


### Monitorar pagamentos para sua Organização em tempo real
Escuta pagamentos para sua Organização em tempo real que forem realizados a partir da chamada do endpoint. 

`GET /payments`

#### Headers
Nome | Valor
---- | -----
Accept | text/event-stream

#### Retorno
A cada evento, será emitido uma instância do tipo [PaymentReceipt](#payment-receipt) 


### Monitorar pagamentos para uma Conta em tempo real
Escuta pagamentos para uma Conta filha em tempo real que forem realizados a partir da chamada do endpoint.

`GET /accounts/:id/payments`

#### Headers
Nome | Valor
---- | -----
Accept | text/event-stream

#### Retorno
A cada evento, será emitido uma instância do tipo [PaymentReceipt](#payment-receipt) 


## Ações

Atualmente existem três tipos de Ações:

- **Criar nova Conta**
- **Emitir um Ativo**
- **Realizar um Pagamento**

Todas elas ocorrem em uma Blockchain / DLT e possuem um Recibo de inclusão na rede. Um Recibo possui três campos:

- `id`: Identificador único da rede. Este campo pode ser utilizado em qualquer Explorer da rede Blockchain de origem.
- `created_at`: Data de criação.
- `type`: Tipo da Ação que representa.   


### Criar nova Conta
Cria uma nova Conta filha. Todos os Ativos criados até então serão suportados por ela automaticamente.

`POST /accounts`

#### Retorno
[AccountResponse](#account-response)


### Emitir um Ativo

<aside class="notice">No momento esta funcionalidade ainda não está aberta. Entre em contato conosco para mais detalhes.</aside>


### Realizar um Pagamento
Realiza um Pagamento a partir de sua Organização ou uma Conta filha para uma conta que suporte o Ativo escolhido.

`POST /payments`

#### Body
[[]PaymentOperations](#payment-operations)

#### Retorno
[PaymentsResponse](#payments-response)
 
## Tratamento de Erros

