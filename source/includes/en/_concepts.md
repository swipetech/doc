# Concepts


## Assets

Assets represent physical or digital forms of value that can be transferred between people or companies. Some examples:

- Fiat currencies (USD, CNY)
- Digital currencies (security tokens)
- Points in a loyalty program
- Mileage
- Securities (shares, bonds)
- Commodities

## Accounts

An Account holds balances of one or more Assets. Accounts are represented by an unique identifier.

In most applications, Accounts usually represent individual users.

## Organizations

An Organization is a particular type of Account that can [issue Assets](#issue-an-asset), [create children Accounts](#create-a-new-account) and [transfer Assets](#transfer-assets) held by those accounts.
Organizations have one or more credential pairs (API Key and Secret).

Organizations usually represent the company that provides the application for users.

## Actions

There are four kinds of Actions:

- Create a new Account
- Destroy an Account
- Issue an Asset
- Transfer Assets

**All actions are performed in a [DLT network](#dlt-networks), causing every change in the data to be automatically registered. Accordingly, every Action generates a [Receipt](#receipt) that proves its inclusion in the network.**

### Create a new Account

Creates a new child Account of your Organization, specifying which Assets it will support.

### Destroy an Account

Removes an Account, rendering its ID unusable. This Action requires the Account to hold empty balances of all its Assets. Destroyed Accounts **cannot** be recovered.

### Issue an Asset

Creates a new Asset. The maximum issuing amount can be limited, making the Asset scarce.

### Transfer Assets

Performs a transfer of an Asset between Accounts or between an Organization and an Account. If the transferred Asset is not supported by the receiving Account, it will start to support it automatically.

## Action Batch

Some Actions can be sent and processed in a batch, in such a way that all Actions are processed simultaneously. That way, if any Action fails for any reason, every other Action will also fail.

**There can be more than a single type of Action in the same batch.**

Batchable Actions:

- Create a new Account
- Issue an Asset
- Transfer Assets

## Memo

Every [Action Batch](#action-batch) supports a optional Memo field that can be used to add any kind of information related to a Batch. A few examples are to include a receipt from a external bank transaction, or add important information about the involved parties.
