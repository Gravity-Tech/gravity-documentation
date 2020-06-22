# Gravity Node Architecture overview

1. Gravity node system types.
2. Tendermint nodes.
3. Blockchain accounts.
4. Schedulers.
5. Ledger nodes.
6. Communicators.
7. CLI interface.
8. Extractors.

## Gravity node system types

![](.gitbook/assets/gh-node-types.png)



Types namespace contains _global_ and _reusable_ interfaces/types that are being used all over the application. If type doesn't conform to specific client interface the _inheritance_ should be used. Consider such common types as:

| Name | Description |
| :--- | :--- |
| **SchedulerTask** | represents task management entity for **MainScheduler** |
| **Asset** | common type for asset representation in specific blockchain. |
| **Account** | common type for account/address representation in specific blockchain. Must have at least address, private & public keys. |
| **Data** | `[]bytes` type alias |
| **Price** | common type for price data |
| **BinancePrice** | type \(price subtype\) that represents only binance price data |

## Tendermint node types

![](.gitbook/assets/tendermint.png)

Tendermint offers BFT algorithm usage. Has its own API. We can communicate with its nodes by RPC protocol. Has its own system types. \(**Commit**, **Block**, **Validator**, **Evidence**\)

Models reference:

[Swagger UI](https://docs.tendermint.com/master/rpc/)

## Blockchain accounts

![](.gitbook/assets/b-accounts.png)

Namespace for account management \(store, delete, update\) for distinct chain. Must conform to **Account** type by design. Communication is provided using _Factory Method_ design pattern.

In a nutshell, distinct account creation is made by calling separate method.

Dictionary contains available list of concrete blockchain accounts for instantiation.

## Schedulers

![](.gitbook/assets/scheduler.png)

Controller for task management. By design GH node needs only one task scheduler - **MainScheduler**. Basic FIFO allows sequential tasks submission & execution. 

Methods reference:

1. getInstance - allows to access reference to global MainScheduler instance
2. getCurrentTask - returns current task
3. scheduleTask - adds task to queue, returns the result\(either added successfuly or not\)

The usability is provided by usage of _Singleton pattern_.

## Ledger nodes \(Core\)

Ledger node can be considered as wrapper of _Tendermint_. That is why ledger node connection to tendermint API is _composed of_  of _tendermint_ because it cannot exist without it or  that ledger node is _tightly coupled_.

Ledger node declares methods that allow GH node to communicate with each other for different purposes: 1. Reaching consensus 2. Gravity score 3. Validation of data

## Communicators

![](.gitbook/assets/communicators.png)

Allow to communicate with specific chains. The most important

The usage must provided by _Bridge pattern_ due to existence specific environment in most chains. **Ethereum** has several network with different conditions. However, the API is still the same. Such behaviour is suitable for _Bridge pattern_. We are not tight for a specific implementation. Distinction of interfaces provide ease of expanding.

## CLI interface

![](.gitbook/assets/client-cli.png)

CLI interface provides access to methods available for client:

1. Node, height monitoring. 
2. Specific chain info. 
3. Current scheduler tasks.
4. Available & connected communicators.
5. Available & connected extractors.

## Extractors

![](.gitbook/assets/extractors.png)

Extractor operate by **Bridge pattern**. The problem is exactly similar to communicator's entity. However, if there was a chain requirement, _the extractor interface is solely declared by GH node_. It helps to distinct specific data extractors.

