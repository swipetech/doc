# Conceitos-chave

## Conta

Uma Conta possui saldos de um ou mais Ativos. Ela é representada por um identificador único.  

## Ativo

Um Ativo é algo que representa um valor (físico ou digital), possui regras de negócio customizáveis e pode ser transferido entre pessoas ou empresas. Alguns exemplos:
 
- Moedas fiduciárias
- Criptomoedas
- Milhas
- Pontos de um programa de fidelidade
- Títulos financeiros
- Crédito
- Commodities

## Organização

Uma Organização é um tipo de [Conta](#conta) especial que pode [Emitir Ativos](#acao), [criar Contas filhas](#acao) e realizar [Pagamentos](#acao).
Ela possui um ou mais pares de credenciais (Api Key e Secret)

## Ação

Ação é a maneira de criar ou modificar algum recurso, como Ativos, Pagamentos e Contas. 

Atualmente existem três tipos de Ações:

- **Criar nova Conta**
- **Emitir um Ativo**
- **Realizar um Pagamento**

Todas elas ocorrem em uma Blockchain / DLT e possuem um [Recibo](#receipt) que garante a inclusão na rede.

### Criar nova Conta 

Cria uma nova Conta filha da sua Organização. Por padrão, todos os Ativos emitidos pela sua Organização já são suportados pela nova Conta.

### Emitir um Ativo

Cria um novo Ativo. Quando criado, sua Organização e Contas filhas o suportam automaticamente.

### Realizar um Pagamento

Realiza um Pagamento com uma ou mais Operações. Cada Operação representa uma transferência de um Ativo entre Contas ou Organizações. 
Todas elas são executadas ao mesmo tempo e, se qualquer uma delas falhar, nenhuma é executada.
