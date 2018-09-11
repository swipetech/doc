# Utilização

Todos os endpoints seguem um padrão de resposta único. Um campo obrigatório `data` e um `error` opcional. 
Para evitar repetição, pode-se assumir que todos os retornos descritos abaixo são contidos dentro do campo `data`. 
Para entender como lidar com erros, leia a seção sobre [tratamento de erros](#tratamento-de-erros) 

## Buscar informações

Utilize os endpoints abaixo para buscar mais detalhes sobre sua Organização, suas Contas ou Ativos.

### Organização
Informações sobre sua Organização.

`GET /organizations`

#### Retorno
[OrganizationResponse](#organization-response)


### Todas as Contas
Busca informações sobre todas as Contas já criadas pela sua Organização.

`GET /accounts`

#### Retorno
[AccountsResponse](#accounts-response)


### Uma Conta específica
Traz informações sobre uma Conta específica criada pela sua Organização.

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
Traz todos os Pagamentos em que o destinatário é sua Organização.

`GET /payments`

#### Retorno
[PaymentsResponse](#payments-response)


### Todos os Pagamentos para uma Conta
Traz todos os Pagamentos em que o destinatário é uma de suas Contas filhas.

`GET /accounts/:id/payments`

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
id | ID da Conta

#### Retorno
[PaymentsResponse](#payments-response)


### Informações sobre um Pagamento específico
Traz informações sobre um Pagamento relacionado à sua Organização ou Contas filhas. 

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
Escuta pagamentos para sua Organização em tempo real, que forem realizados a partir da chamada do endpoint. 

`GET /payments`

#### Headers
Nome | Valor
---- | -----
Accept | text/event-stream

#### Retorno
A cada evento, será emitido uma instância do tipo [PaymentReceipt](#payment-receipt) 


### Monitorar pagamentos para uma Conta em tempo real
Escuta pagamentos para uma Conta filha em tempo real, que forem realizados a partir da chamada do endpoint.

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

