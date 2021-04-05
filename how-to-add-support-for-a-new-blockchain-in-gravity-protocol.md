# How to add support for a new blockchain in Gravity Protocol

## Writing extensions

In order to extend Gravity Protocol by adding support for a new blockchain, some commits are expected into Gravity-Tech/gravity-core and a few other repos mentioned below. Those changes can be divided into:

1. Gravity Ledger Node logic \(adaptor\).
2. Gravity Oracle logic.
3. Gravity Core contracts \([Gravity-Tech/gravity-core/contracts](https://github.com/Gravity-Tech/gravity-core/tree/master/contracts)\).
4. Gravity Deployer. \(Gravity-Tech/gateway-deployer\)

We highly recommend that you open pull requests in specific repositories mentioned in the guide while implementing your solution, which should minimize the rewriting of the existing codebase. If your aim is to support application layer logic on top of the new supported chain, you need to commit changes to [Gravity-Tech/gateway](https://github.com/Gravity-Tech/gateway). That part of logic includes writing an appropriate USER-SC \(user smart contract\). 

The first alpha product built on top of Gravity is SuSy, a protocol and interface for crosschain gateways with the Waves as origin and Binance Smart Chain as the destination chain. SuSy mainnet alpha is currently live at susy.gravity.tech and provides an interface that allows users to swap USDN tokens from Waves into BSC. USDNs on BSC incorporate an identical functionality as on Ethereum, including auto-staking, elastic supply, and staking rewards that are automatically distributed among all holders. SuSy is a second layer protocol over[ Gravity](https://gravity.tech/), and it prescribes an implementation of cross-chain transfers of digital assets \(tokens\) in blockchain networks that support smart contracts, focused primarily on popular blockchains with varying architectures, consensuses and cryptography. SuSy is centered exclusively around technical implementation of transfers, without bringing any incentive models for cross-chain transfer providers.

## SuSy Protocol

The SuSy protocol is based on trust in the oracle, which is an intermediary in the transfer of information from one blockchain to another. From a technical standpoint, when implementing the oracle as a trustless decentralized system, which is what the[ Gravity](https://arxiv.org/abs/2007.00966) protocol does, cross-chain gateways on top of it inherit the trustlessness. Another feature of the SuSy protocol implementation over the Gravity oracle protocol is the presence of useful high-level abstractions and services.

Furthermore, for cross-chain swap of a token from one blockchain to another, no additional tokens are required, except for native tokens of the corresponding blockchain networks.

Let us introduce key terminology:

ORIGIN-CHAIN: a blockchain network from which the transfer originates. That is, in this network, tokens are blocked and unblocked.

DESTINATION-CHAIN: a blockchain, to which transfers are made from the ORIGIN-CHAIN. Issuance and burning of wrapped tokens take place on this network.

sw{TOKEN}: SuSy-wrapped token, a token issued on the DESTINATION-CHAIN ​​blockchain. For example, swETH on networks other than Ethereum mainnet.

IB-PORT is a smart contract in DESTINATION-CHAIN ​​that implements the functionality of issuance and burning of sw{TOKEN}.

LU-PORT is a smart contract in ORIGIN-CHAIN that locks and unlocks the original token.

NEBULA-SC is one of the main architectural units of the Gravity protocol, a smart contract that accepts and verifies data from Gravity oracles. It implements checks of data relevance \(blockchain height\), availability of appropriate cryptographic signatures and threshold signature rules for transmitted data.

USER-SC is one of the main architectural units of the Gravity protocol. It is a smart contract that accepts data verified in NEBULA-SC and produces an action that is part of a custom application. In the case of SuSy, LU-PORT and IB-PORT are examples of USER-SC.

PULSE-TX is a transaction that will transfer hash from data to NEBULA-SC with necessary signatures for verification and registration.

SEND-DATA-TX is a transaction that transfers data verified and registered in NEBULA-SC to USER-SC.

EXTRACTOR is a Gravity network service that reads and interprets data from external sources. In the case of the SuSy protocol, it reads and interprets data from transactions and states associated with LU-PORT and IB-PORT of the corresponding blockchain networks.

SWAP-ID is a unique identifier for the cross-chain transfer swap operation. For each SWAP-ID, there is a sender in ORIGIN-CHAIN, receiver in DESTINATION-CHAIN, amount and status \(registered / processed / finalized\). The final status is determined by Gravity oracles, and a rule for setting the status may differ in different blockchain networks.

STATUS-CONTROLLER is a mechanism for managing the status of SuSy-SWAP-ID, the ability to unlock or issue a token if, for some reason, this did not happen in a standard way within a certain time interval \(for example, due to high gas fees or blockchain forks\).

SUSY-GATEWAY is a set of services and smart contracts that support the transfer of data on the lock/unlock or issue/burn statuses of transferred tokens.

Below is the description of SuSy cross-chain transfer algorithm that uses an example of a transfer from ORIGIN-CHAIN ​​to DESTINATION-CHAIN, where it will be issued as a swT \(wrapped\) token and sent to the recipient R in DESTINATION-CHAIN.

SuSy origin-destination cross-chain transfer scheme.

A user \(S\) interacts with the LU-PORT smart contract by transferring an amount \(A\) of the T token to it and specifying the recipient’s public address in DESTINATION-CHAIN. The gateway smart contract automatically creates a unique SWAP-ID and sets the registered status. The received funds are blocked on the LU-PORT smart contract.

Information about this event is registered by extractors, the Gravity network’s service that processes the received data and communicates it to Gravity. From the Gravity framework, the oracle moves hashed data about the new SWAP-ID and directions to the verification contract \(NEBULA-SC\), in which the signatures of the Gravity network validators and the legitimacy of the transferred context are checked.

Upon verification, the SEND-DATA-TX transaction is called, containing a set of data and instructions for issuing and sending swT tokens to the recipient \(R\).

Likewise, data about this event is handled by Gravity network oracles, and, contingent upon successful execution, the “processed” status is set. After reaching a certain number of blocks at which the likelihood of a fork is minimal, it may be necessary to set the finalized status.

In the opposite direction, for transferring the swT token from DESTINATION CHAIN ​​to ORIGIN CHAIN ​​and unlocking T on the LU PORT contract, the procedure is similar. The only difference is in the final transactions, that is, burning the swT token on IB PORT and unlocking the T token on LU PORT, are reversed.

## Smart Contracts

As regards extending the SuSy dApp onto other blockchains, USER-SC plays the role of IB \(Issue-Burn\) and LU \(Lock-Unlock\) ports. USER-SC methods are meant to be invoked by NEBULA-SC. So, in order to have a working application on top of the protocol, you need to:

1. Provide NEBULA-SC, SYSTEM-SC \(gravity contract\) inside the Gravity-Tech/gravity-core/contracts.
2. Create a custom USER-SC, which corresponds to your custom NEBULA-SC.

Therefore, to ensure compatibility with a new chain, a set of separate smart contracts is required. More importantly, there are two types of contracts: those used by the core of Gravity and application-specific contracts \(e.g. SuSy\). 

An example of an IB Port for Ethereum and Waves is below: [https://github.com/Gravity-Tech/gateway/blob/main/contracts/ethereum/IBPort.sol](https://github.com/Gravity-Tech/gateway/blob/main/contracts/ethereum/IBPort.sol) 

[https://github.com/Gravity-Tech/gateway/blob/main/contracts/waves/luport.ride](https://github.com/Gravity-Tech/gateway/blob/main/contracts/waves/luport.ride)

There should be two new smart contracts in the new blockchain network with the following functionality:

1. The smart contract should support the external call of the attachEventData function with the following parameters:
   1. Token ID \(optional since different tokens will have their own gateways\)
   2. Amount
   3. Receiver
2. Only one of 5 admin accounts can call this method.
3. After attachEventData, Receiver must receive the wrapped tokens
4. Holders of wrapped tokens should be able to send tokens to the gateway address, which should trigger API \(RPC\) calls.

For all statements 1-4, an API must be implemented \(open and public\).

Consider checking the “contracts” directory inside the Gravity-Tech/gateway repository to see examples of gateway contracts’ source code.

After creating the contracts, they need to be compiled into bytecode. The compiled bytecode files should be put into the “/abi” directory.

## Adaptors

In order to add an implementation for any new chain, it must first be supported by the Gravity Network itself. After completing the smart contracts, it is necessary to implement core adaptors within the [gravity-core](https://github.com/Gravity-Tech/gravity-core) repository. 

To get a general idea of the required changes, review this[ "BSC support" pull request](https://github.com/Gravity-Tech/gravity-core/pull/32/files) for Gravity Ledger Node extension. We are going to review each part of the changes described in the pull request above.

The fundamental architecture of Gravity Core application provides a distinct modular logic without tight coupling. This guarantees the ease of extending separate components. Moreover, Golang does not allow import cycles. Any programming language is welcome, however it is recommended to extend the existing Go source code by providing implementations for required methods rather than rewriting from scratch.

Key things to keep in mind:

* Take notice of the BlocksInterval parameter for the specific chain \(required for a correct network “pulse”\).
* Include an implementation of the adaptors.IBlockchainAdaptor interface, which is the most descriptive part of each specific chain.

Test recommendations: try deploying contracts & starting a node in customnet to test out your newly written adapter. After testing, create a pull request to Gravity-Tech/gravity-core.

#### cmd/gravity/commands/ledger.go

In the createApp function, a constructor for instantiation of a new adaptor should be provided. Consider putting your adaptor implementation in common/adaptors/\[chain\_name\].go. 

#### common/adaptors/\[chain\_name\].go

The most important part regarding communication with a specific chain is the IBlockchainAdaptor interface. 

#### common/account/chain.go

Here, a constant should be provided and its behaviour in places where existing constants are mentioned should be implemented too.

#### common/account/nebula.go

This part is responsible for Nebula logic.

#### common/account/pubkey.go

Here, chain behaviour regarding account address instantiation is specified.

#### common/storage/consuls.go

This part is responsible for parsing of consul keys.

## The Oracle component

#### oracle/node/node.go

A crucial part of Gravity Oracle. node.go, as well as the entire oracle/node module, describes existing implementations and sets up oracle constraints.

## Reference

1. [https://github.com/Gravity-Tech/gateway](https://github.com/Gravity-Tech/gateway)
2. [https://github.com/Gravity-Tech/gravity-core](https://github.com/Gravity-Tech/gravity-core)
3. [https://github.com/Gravity-Tech/gateway-deployer](https://github.com/Gravity-Tech/gateway-deployer)
4. [Gravity Protocol WP](https://arxiv.org/pdf/2007.00966.pdf)

## Useful links

Gravity White Paper – [https://arxiv.org/pdf/2007.00966.pdf](https://arxiv.org/pdf/2007.00966.pdf) 

SuSy White Paper – [https://arxiv.org/pdf/2008.13515.pdf](https://arxiv.org/pdf/2008.13515.pdf)

Gravity Tech Github – [https://github.com/Gravity-Tech](https://github.com/Gravity-Tech)

Hackathon/Grant Application Form – [https://github.com/Waves-Association/grants-program/issues](https://github.com/Waves-Association/grants-program/issues)  
  
  
  


