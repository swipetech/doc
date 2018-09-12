# Conceitos-chave

## Conta

Uma Conta possui saldos de um ou mais Ativos. Ela é representada por um identificador único.  

## Ativo

Um Ativo é algo que representa um valor (físico ou digital), possui regras de negócio customizáveis e pode ser transferido entre pessoas ou empresas. Alguns exemplos:
 
- Moedas fiduciárias
- Modeas digitais
- Criptomoedas / Tokens
- Milhas
- Pontos de um programa de fidelidade
- Títulos financeiros
- Crédito
- Commodities
- Moedas de jogos

## Organização

Uma Organização é um tipo de [Conta](#conta) especial que pode [Emitir Ativos](#acao), [criar Contas filhas](#acao) e realizar [Pagamentos](#acao).
Ela possui um ou mais pares de credenciais (Api Key e Secret)

## Ação

Ação é a maneira de criar ou modificar um ou mais recursos, como Ativos, Pagamentos e Contas. 

Atualmente existem três tipos de Ações:

- Criar nova Conta
- Emitir um Ativo
- Realizar um Pagamento

**Todas elas ocorrem em uma Blockchain / DLT e possuem um [Recibo](#receipt) que garante a inclusão na rede. 
Por esse motivo, o tempo de resposta de cada Ação é diretamente proporcional ao [tempo de confirmação na rede de origem](#blockchain-dlt)**

### Criar nova Conta 

Cria uma nova Conta filha da sua Organização. Por padrão, todos os Ativos emitidos pela sua Organização já são suportados pela nova Conta.

### Emitir um Ativo

Cria um novo Ativo. Quando criado, sua Organização e Contas filhas o suportam automaticamente.

### Realizar um Pagamento

Realiza um Pagamento com uma ou mais Operações. Cada Operação representa uma transferência de um Ativo entre Contas ou Organizações.    
Uma Ação de Pagamento é atômica, isto é, todas as operações são executadas ao mesmo tempo e, se qualquer uma delas falhar, nenhuma é executada.

<aside class="notice">Atualmente um Pagamento suporta até 100 Operações. Se tentar ultrapassar este limite, um Erro com código <code>max_ops_limit_exceeded</code> será retornado.</aside>
