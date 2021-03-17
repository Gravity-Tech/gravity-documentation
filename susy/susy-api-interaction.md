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
    * assetId: DG2xFkPdDwKUoBkzGAhQtLpSGzfXLiCYPEzeKH2Ad24p
    * lu-port: 3PEXiW1BrBNMo5A9dfj2CnBW2mwMiaf2sAe
  * BSC
    * token: 0xc4b6f32b84657e9f6a73fe119f0967be5ba8cf05
    * ib-port: 0x8c0e11a6E692d02f71598AB5050083ED691Eb760
* gwaNSBT
  * Waves 
    * assetId: 6nSpVyNH7yM69eg446wrQR94ipbbcmZMU1ENPwanC97g
    * lu-port: 3PRGPGtsVZVUCFRsEKp1FHccv6uFu8YNqb1
  * BSC
    * token: 0xaDb688CC2D5A729d7e5ddEcDA8B63ED118F41eA4
    * ib-port: 0xf427525Eb648d14c1Da28E530e9fe7ab9832c411
* NSBT Legacy
  * Waves 
    * assetId: 6nSpVyNH7yM69eg446wrQR94ipbbcmZMU1ENPwanC97g
    * lu-port: 3PPUsj1yjMMAAg2hihdebK7n8zkAagHqdNT
  * BSC
    * token: 0x496d451dDAB0F79346f773CbC2eb7Aee58446019
    * ib-port: 0x59622815BADB181a2c37052136a9480C6A4a4eA6

## Contracts source

* evm-based [ib-port](https://github.com/Gravity-Tech/gateway/blob/main/contracts/ethereum/IBPort.sol)
* evm-based [token](https://github.com/Gravity-Tech/gateway/blob/main/contracts/ethereum/Token.sol)
* waves-based [lu-port](https://github.com/Gravity-Tech/gateway/blob/main/contracts/waves/luport.ride)
* evm-based [abi directory](https://github.com/Gravity-Tech/gateway/tree/main/abi/ethereum)
* waves [base64 directory](https://github.com/Gravity-Tech/gateway/tree/main/abi/waves)

## Waves-BSC with waves-transactions

The code below demonstrates swapping USDN from Waves to BSC using [waves-transactions](https://github.com/wavesplatform/waves-transactions) and SuSy protocol's contracts directly.

```javascript
const { broadcast, invokeScript } = require('@waves/waves-transactions');

const nodeUrl = 'https://nodes.wavesnodes.com';

const seed = ""

const luportAddress    = ""
const recipientAddress = ""
const usdnAssetId      = "DG2xFkPdDwKUoBkzGAhQtLpSGzfXLiCYPEzeKH2Ad24p"
const amount           = 100000

const tx = invokeScript({dApp: luportAddress,
                         call: {function: "createTransferWrapRq",
                                args: [{type: "string",
                                        value: recipientAddress}]},
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

