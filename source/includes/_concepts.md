# Conceitos

## Conta

Uma Conta possui saldos de um ou mais Ativos. Ela é representada por um identificador único.  

## Ativo

Um Ativo representa valores físicos ou digitais. Possui regras de negócio customizáveis e pode ser transferido entre pessoas ou empresas. Alguns exemplos:

- Moedas fiduciárias
- Moedas digitais (tokens)
- Criptomoedas
- Milhas
- Pontos de um programa de fidelidade
- Títulos financeiros (ações, debêntures)
- Commodities

## Organização

Uma Organização é um tipo de Conta especial que pode [emitir Ativos](#emitir-um-ativo), [criar Contas filhas](#criar-nova-conta) e [transferir os Ativos](#transferir-ativos) dessas contas.
Organizações possuem um ou mais pares de credenciais (API Key e Secret).

## Ação

Chamamos de Ação a maneira de criar ou modificar um ou mais recursos, como Ativos, Transferências e Contas.

Atualmente existem três tipos de Ações:

- Criar nova Conta
- Destruir uma Conta
- Emitir um Ativo
- Transferir Ativos

**Todas as Ações ocorrem em uma rede DLT e possuem um [Recibo](#receipt) como prova de sua inclusão na rede.
Por esse motivo, o tempo de resposta de cada Ação é diretamente proporcional ao [tempo de confirmação na rede de origem](#blockchain-e-dlt).**

### Criar nova Conta

Cria uma nova Conta filha da sua Organização. Por padrão, todos os Ativos emitidos pela sua Organização já são suportados pela nova Conta.

### Destruir uma Conta

Remove uma Conta a partir de seu ID. Para esta ação é necessário que a conta não possua saldos de nenhum ativo.

<aside class="warning">Essa Ação é destrutiva e não pode ser desfeita.</aside>

### Emitir um Ativo

Cria um novo Ativo. Quando criado, sua Organização e Contas filhas o suportam automaticamente.

### Transferir Ativos

Executa uma transferência de um Ativo entre Contas ou Organização.

## Lote de Ações

Algumas Ações podem ser enviadas e processadas em lote. Isto faz com que todas sejam processadas ao mesmo tempo e, se qualquer uma falhar por algum motivo, todas falharão.

**Um lote pode incluir mais de um tipo de Ação simultaneamente.** 

Segue a lista de Ações que suportam essa funcionalidade:

- Criar nova Conta
- Emitir um Ativo
- Transferir Ativos
