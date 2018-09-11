# Utilização

Todos os endpoints seguem um padrão de resposta único. Um campo obrigatório `data` e um `error` opcional. 
Para evitar repetição, pode-se assumir que todos os retornos descritos abaixo são contidos dentro do campo `data`. 
Para entender como lidar com erros, leia a seção sobre [tratamento de erros](#tratamento-de-erros) 

## Buscar informações

Utilize os endpoints abaixo para buscar mais detalhes sobre sua Organização, suas Contas ou Ativos.

### Organização

`GET /organizations`

#### Retorno


### Todas as Contas criadas pela sua Organização

`GET /organizations/accounts`


### Informações sobre uma Conta específica

`GET /organizations/accounts/:id`

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
id | ID da Conta 


### Todos os Ativos criados pela sua Organização

`GET /organizations/assets`


### Monitorar Pagamentos para sua Organização

`GET /organizations/payments`


### Monitorar Pagamentos para uma Conta

`GET /organizations/accounts/:id/payments`

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
id | ID da Conta


### Informações sobre um Pagamento específico

`GET /payments/:ID`

#### Parâmetros de URL

Parâmetro | Descrição
--------- | -----------
id | ID do Pagamento 


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

`POST /organizations/accounts`


### Emitir um Ativo

<aside class="notice">No momento esta funcionalidade ainda não está aberta. Entre em contato conosco para mais detalhes.</aside>


### Realizar um Pagamento

`POST /payments`

#### Body

Parâmetro | Tipo | Descrição
--------- | ---- | ---------
from | string | ID da Conta remetente 
to | string | ID da Conta destinatária
asset | string | ID do Ativo do Pagamento
amount | float64 | Montante a ser transferido

 
## Tratamento de Erros

