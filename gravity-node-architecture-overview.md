# Gravity Node Architecture

## Gravity node system types

![](.gitbook/assets/gh-node-types.png)



The Types Namespace contains _global_ and _reusable_ interfaces/types that are used universally in the application. If a type does not conform to a specific client interface, _inheritance_ should be used. The most common types are listed below:

| Name | Description |
| :--- | :--- |
| **SchedulerTask** | represents task management entity for **MainScheduler** |
| **Asset** | common type for asset representation in a specific blockchain |
| **Account** | common type for account/address representation in a specific blockchain. Must have an address, private & public keys. |
| **Data** | `[]bytes` type alias |
| **Price** | common type for price data |
| **BinancePrice** | type \(price subtype\) that represents Binance price data |

## Tendermint node types

![](.gitbook/assets/tendermint.png)

Tendermint offers BFT algorithm usage and has its own API. It is possible to communicate with Tendermint nodes via the RPC protocol. Tendermint also has its own system types. \(**Commit**, **Block**, **Validator**, **Evidence**\)

Models reference:

[Swagger UI](https://docs.tendermint.com/master/rpc/)

## Blockchain accounts

![](.gitbook/assets/b-accounts.png)

There is a separate namespace for account management \(store, delete, update\) for each distinct chain. It must conform to the **Account** type by design. Communication is provided using the _Factory Method_ design pattern.

In a nutshell, creation of a new account is made by calling a separate method.

The dictionary contains the available list of concrete blockchain accounts for instantiation.

## Schedulers

![](.gitbook/assets/scheduler.png)

Controller for task management. By design, Gravity node only needs one task scheduler: **MainScheduler**. Basic FIFO allows for submission & execution of sequential tasks.

Methods reference:

1. getInstance - allows to access reference to global MainScheduler instance
2. getCurrentTask - returns current task
3. scheduleTask - adds task to queue, returns the result

The usability is provided by the usage of a _Singleton pattern_.

## Ledger nodes \(Core\)

A ledger node can be considered as a wrapper of _Tendermint_. Therefore, a ledger node's connection and the Tendermint API are _tightly coupled_.

A ledger node declares methods that allow a Gravity node to communicate with each other for different purposes: 1. Reaching consensus 2. Gravity score 3. Validation of data

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

Extractors operate by **Bridge pattern**. The problem is exactly similar to communicator's entity. However, if there was a chain requirement, _the extractor interface is solely declared by GH node_. It helps to distinct specific data extractors.

