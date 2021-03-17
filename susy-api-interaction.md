---
description: This page is currently under development.
---

# SuSy API Interaction

[SuSy](https://susy.gravity.tech), a protocol built on top of Gravity for wrapping tokens, works on a port-to-address basis: when Bob wants to swap 100 USDN from _wavesAddressBob_ to _bscAddressBob_, Bob should send 100 USDN and _bscAddressBob_ to the “lock” function on _ethAddressPort_. Bob should then receive 100 gwUSDN on _bscAddressBob_.

To automate swaps on the SuSy bridge, users should call “lock” or “burn” functions on source chains.

To interact with EVM-based source networks, users need to utilize web3 libraries that can wrap the “eth\_SendRawTransaction'' call from Ethereum JSON RPC API. The documentation below contains ports’ addresses, contract interfaces and function parameters that can facilitate interaction with SuSy protocol.

## Addresses

* USDN
  * Waves 
    * assetId: _DG2xFkPdDwKUoBkzGAhQtLpSGzfXLiCYPEzeKH2Ad24p_
    * lu-port: _3PEXiW1BrBNMo5A9dfj2CnBW2mwMiaf2sAe_

## Contracts source

* evm-based [ib-port](https://github.com/Gravity-Tech/gateway/blob/main/contracts/ethereum/IBPort.sol)
* evm-based [token](https://github.com/Gravity-Tech/gateway/blob/main/contracts/ethereum/Token.sol)
* waves-based [lu-port](https://github.com/Gravity-Tech/gateway/blob/main/contracts/waves/luport.ride)

## Waves-BSC with waves-transactions

The code below demonstrates swapping USDN from Waves to BSC using [waves-transactions](https://github.com/wavesplatform/waves-transactions) and SuSy protocol's contracts directly.

```javascript
const { broadcast, invokeScript } = require('@waves/waves-transactions');

const nodeUrl = 'https://nodes.wavesnodes.com';

const seed = ""

const luportAddress    = ""
const recepientAddress = ""
const usdnAssetId      = "DG2xFkPdDwKUoBkzGAhQtLpSGzfXLiCYPEzeKH2Ad24p"
const amount           = 100000

const tx = invokeScript({dApp: luportAddress,
                         call: {function: "createTransferWrapRq",
                                args: [{type: "string",
                                        value: recepientAddress}]},
                         payment: [{assetId: usdnAssetId,
                                    amount:  amount}]},
                        seed)

broadcast(tx,nodeUrl).then(resp => console.log(resp));
```

## BSC-Waves web3

The code below demonstrates swapping USDN from BSC to Waves using [web3](https://web3js.readthedocs.io/en/v1.3.4/) and SuSy protocol's contracts directly.

```javascript
require('dotenv').config()

const Web3 = require('web3')
const { base58Decode, base16Encode } = require('@waves/ts-lib-crypto')

const nodeUrl = ""

const tokenAddress  = ""
const tokenAbi      = ""
const ibportAddress = ""
const ibportAbi     = ""

const senderPrivKey = ""

const receiverAddress = "3PG4DtkmcZjPGRpXujbD44ZKydo1D9Y6r2N"
const amount = 1

const web3 = new Web3( new Web3.providers.HttpProvider(nodeUrl) )
web3.eth.defaultAccount = web3.eth.accounts.privateKeyToAccount('senderPrivKey');

const tokenContract = web3.eth.contract(tokenAbi)
const tokenInstance = tokenContract.at(tokenAddress)

await tokenInstance.approve(ibportAddress, amount)

const receiverBytes = base58Decode(receiver)
const receiverHex = '0x' + base16Encode(receiverBytes).padEnd(64, '0')

const ibportContract = web3.eth.contract(ibportAbi)
const ibportInstance = ibportContract.at(ibportAddress)

await ibportInstance.createTransferUnwrapRequest(amount, receiverHex)
```

## SuSy Swagger API

[https://api.graviton.one](https://web3js.readthedocs.io/en/v1.3.4/)

## 

