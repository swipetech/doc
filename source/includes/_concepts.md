# Conceitos


## Ativo

Um Ativo representa valores físicos ou digitais. Possui regras de negócio customizáveis e pode ser transferido entre pessoas ou empresas. Alguns exemplos:

- Moedas fiduciárias (BRL, USD)
- Moedas digitais (tokens)
- Criptomoedas
- Milhas
- Pontos de um programa de fidelidade
- Títulos financeiros (ações, debêntures)
- Commodities

## Conta

Uma Conta possui saldos de um ou mais Ativos. Ela é representada por um identificador único.

Na maioria das aplicações, Contas costumam representar os usuários da aplicação.

## Organização

Uma Organização é um tipo de Conta especial que pode [emitir Ativos](#emitir-um-ativo), [criar Contas filhas](#criar-nova-conta) e [transferir os Ativos](#transferir-ativos) dessas contas.
Organizações possuem um ou mais pares de credenciais (API Key e Secret).

Organizações costumam representar a empresa que disponibiliza a aplicação para os usuários.

## Ação

Existem quatro tipos de Ações:

- Criar nova Conta
- Destruir uma Conta
- Emitir um Ativo
- Transferir Ativos

**Todas as Ações ocorrem em uma [rede DLT](#redes-dlt), de forma que todos as alterações sejam automaticamente registradas na rede. Assim, cada Ação possui um [Recibo](#receipt) que comprova a sua inclusão na rede.**

### Criar nova Conta

Cria uma nova Conta filha da sua Organização. Por padrão, todos os Ativos emitidos pela sua Organização já são suportados pela nova Conta.

### Destruir uma Conta

Remove uma Conta a partir de seu ID. Para esta Ação, é necessário que a conta não possua saldos de nenhum Ativo. Contas destruídas **não** podem ser recuperadas.

### Emitir um Ativo

Cria um novo Ativo. Quando criado, sua Organização e Contas filhas passam a suportá-lo automaticamente.

### Transferir Ativos

Executa uma transferência de um Ativo entre Contas ou Organização.

## Lote de Ações

Algumas Ações podem ser enviadas e processadas em lote. Isso faz que todas as Ações do lote sejam processadas ao mesmo tempo e, se qualquer uma falhar por algum motivo, todas falharão.

**Um lote pode incluir mais de um tipo de Ação simultaneamente.**

Ações que atualmente suportam essa funcionalidade:

- Criar nova Conta
- Emitir um Ativo
- Transferir Ativos
