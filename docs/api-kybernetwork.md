---
id: KyberNetwork
title: KyberNetwork
---
# contract KyberNetwork
is [Withdrawable](api-withdrawable.md), Utils2, [KyberNetworkInterface](api-kybernetworkinterface.md)\
imports ERC20Interface, [KyberReserveInterface](api-kyberreserveinterface.md), [KyberNetworkInterface](api-kybernetworkinterface.md), [Withdrawable](api-withdrawable.md), Utils, WhiteListInterface, ExpectedRateInterface, [FeeBurnerInterface](api-feeburnerinterface.md)

*Source*: [KyberNetwork.sol](https://github.com/KyberNetwork/smart-contracts/blob/master/contracts/KyberNetwork.sol)

The KyberNetwork contract's role is to facilitate two main functionalities, which is to return the expect exchange rate, and to execute a trade.
___

## INDEX

<AUTOGENERATED_TABLE_OF_CONTENTS>

## REFERENCE

### Structs

### `TradeInput`
| Property | Type                  | Description                                                 |
| --------- |:---------------------:|:-----------------------------------------------------------:|
| `trader` | address | trader's address                                  |
| `src`     | ERC20                  | source ERC20 token contract address   |
| `srcAmount`         | uint    | wei amount of source ERC20 token                                     |
| `dest`              | ERC20   | destination ERC20 token contract address                             |
| `destAddress`       | address | recipient address for destination ERC20 token                        |
| `maxDestAmount`     | uint    | limit on the amount of destination tokens                            |
| `minConversionRate` | uint    | minimum conversion rate;  trade is canceled if actual rate is lower |
| `walletId`          | address | wallet address to send part of the fees to                           |
| `hint`         | bytes | internal use by smart contract |
<br />

### `BestRateResult`
| Property | Type                  | Description                                                 |
| --------- |:---------------------:|:-----------------------------------------------------------:|
| `rate` | uint | trader's address                                  |
| `reserve1`     | address                  | reserve address offering best srcToken -> ETH rate  |
| `reserve2`         | address    | reserve address offering best ETH -> destToken rate          |
| `weiAmount`              | uint   | ETH wei amount                             |
| `rateSrcToEth`       | uint | srcToken -> ETH rate                        |
| `rateEthToDest`     | uint    | ETH -> destToken rate                            |
| `destAmount` | uint    | destination token amount |
<br />

### Events

### `AddReserveToNetwork`
Event for logging reserve additions or removals from the network.
___
event __AddReserveToNetwork__(KyberReserveInterface reserve, bool add)
| Parameter | Type                  | Description                                                 |
| --------- |:---------------------:|:-----------------------------------------------------------:|
| `reserve` | KyberReserveInterface | reserve's contract address                                  |
| `add`     | bool                  | `true` if reserve was successfully added, otherwise `false` |
<br />

### `EtherReceival`
Event for logging amount of ETH received from sender.
___
event __EtherReceival__(address indexed sender, uint amount)
| Parameter | Type            | Description      |
| --------- |:---------------:|:----------------:|
| `sender`  | address | sender's address |
| `amount`  | uint            | ETH amount sent  |
<br />

### `KyberProxySet`
Event for logging change in [`KyberNetworkProxy.sol`](api-kybernetworkproxy.md) address
___
event __KyberProxySet__(address proxy, address sender)
| Parameter          | Type            | Description                              |
| ------------------ |:---------------:|:----------------------------------------:|
| `proxy`              | address           | KyberNetworkProxy contract address      |
| `sender`           | address | sender's address                         |
<br />

### `KyberTrade`
Event for logging execution of trades.
___
event __KyberTrade__(address srcAddress, ERC20 srcToken, uint srcAmount, address destAddress, ERC20 destToken, uint destAmount)
| Parameter          | Type            | Description                              |
| ------------------ |:---------------:|:----------------------------------------:|
| `srcAddress`           | address | trader's address                         |
| `srcToken`              | ERC20           | source ERC20 token contract address      |
| `srcAmount`  | uint            | source ERC20 token amount in wei         |
| `destAddress`             | ERC20           | recipient address for destination ERC20 token |
| `destToken`              | ERC20           | destination ERC20 token contract address      |
| `destAmount` | uint            | destination ERC20 token amount in wei    |
<br />

### `ListReservePairs`
Event for logging of listing of token pairs that a reserve is allowed to trade.
___
event __ListReservePairs__(address reserve, ERC20 src, ERC20 dest, bool add)
| Parameter | Type    | Description                                                    |
| ----------|:-------:|:--------------------------------------------------------------:|
| `reserve` | address | reserve's contract address                                     |
| `src`     | ERC20   | source ERC20 token contract address                            |
| `dest`    | ERC20   | destination ERC20 token contract address                       |
| `add`     | bool    | `true` if trades are allowed, otherwise `false` for disallowed |
<br />

### Functions

### `KyberNetwork`
Contract constructor. Note that constructor methods are called exactly once during contract instantiation and cannot be called again.
___
function __KyberNetwork__(address \_admin) public
| Parameter  | Type    | Description            |
| ---------- |:-------:|:----------------------:|
| `_admin`   | address | admin's wallet address |
___
Web3 Example:
```js
const fs = require('fs');
const solc = require('solc');
const Web3 = require('web3');

const web3 = new Web3(new Web3.providers.HttpProvider('http://localhost:8545'));
const input = fs.readFileSync('KyberNetwork.sol', 'utf8');
const output = await solc.compile(input, 1);
const bytecode = output.contracts['KyberNetwork'].bytecode;
const abi = JSON.parse(output.contracts['KyberNetwork'].interface);

const KyberNetwork = new web3.eth.Contract(JSON.parse(abi));
const _admin = '<ADMIN ADDRESS>';

const deploy = KyberNetwork.deploy({
  data: `0x${bytecode}`,
  arguments: [
    _admin
  ]
});

broadcastTx(deploy)
```
Code snippet reference: [broadcastTx()](appendix-codes.md#broadcasting-tx)

<br />

### `addReserve`
Adds or deletes a reserve to or from the network. Only admin can invoke.
___
function __addReserve__(KyberReserveInterface reserve, bool add) public onlyAdmin
| Parameter | Type                  | Description                                      |
| --------- |:---------------------:|:------------------------------------------------:|
| `reserve` | KyberReserveInterface | reserve's contract address                       |
| `add`     | bool                  | `true` to add reserve, `false` to remove reserve |
Modifiers: [onlyAdmin](api-permissiongroups.md#onlyadmin)
___
Web3 Example:
```js
const reserve = '0x63825c174ab367968EC60f061753D3bbD36A0D8F';
const add = true;

let transactionData = KyberNetwork.methods.addReserve(
  reserve,
  add
).encodeABI()

txReceipt = await web3.eth.sendTransaction({
    from: ADMIN_ADDRESS,
    to: KYBER_NETWORK_ADDRESS,
    data: transactionData
})
```

<br />

### `enabled`
Determine the state of the network (Eg. disabled when smart contracts are upgraded)
___
function __enabled__() public view returns (bool)
**Returns:**\
`true` if network is enabled, `false` otherwise
___
Web3 Example:
```js
let networkEnabled = await KyberNetwork.methods.enabled().call()
```
<br />

### `findBestRate`
Finds the best conversion rate for a pair of tokens. If several reserves have small rate differences, pick one at random.
___
function __findBestRate__(ERC20 src, ERC20 dest, uint srcAmount) public view returns (uint obsolete, uint rate)
| Parameter | Type  | Description                              |
| --------- |:-----:|:----------------------------------------:|
| `src`     | ERC20 | source ERC20 token contract address      |
| `dest`    | ERC20 | destination ERC20 token contract address |
| `srcAmount`  | uint  | wei amount of source ERC20 token         |
**Returns:**\
Index of the best reserve (depreciated) and the best exchange rate for the pair
___
Web3 Example:
```js
const src = '0xeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeee'; // ETH
const dest = '0xdd974D5C2e2928deA5F71b9825b8b646686BD200' // KNC
const srcQty = new web3.utils.BN('3000000000000000000000');
result = await KyberNetwork.methods.findBestRate(
  src,
  dest,
  srcQty
).call()

rate = result[1]
```
<br />

### `getExpectedRate`
Get the expected exchange rate.
___
function __getExpectedRate__(ERC20 src, ERC20 dest, uint srcQty) public view returns (uint expectedRate, uint slippageRate)
| Parameter | Type  | Description                              |
| --------- |:-----:|:----------------------------------------:|
| `src`     | ERC20 | source ERC20 token contract address      |
| `dest`    | ERC20 | destination ERC20 token contract address |
| `srcQty`  | uint  | wei amount of source ERC20 token         |
**Returns:**\
The expected exchange rate and slippage rate
___
Web3 Example:
```js
const src = '0xeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeee' // ETH
const dest = '0xdd974D5C2e2928deA5F71b9825b8b646686BD200' // KNC
const srcQty = new web3.utils.BN('3000000000000000000000')

let result = await KyberNetwork.methods.getExpectedRate(
  src,
  dest,
	srcQty
).call()

let expectedRate = result[0]
let slippageRate = result[1]
```
<br />

### `getNumReserves`
Get the total number of reserves in the network.
___
function __getNumReserves__() public view returns (uint)\
**Returns:**\
Total number of reserves
___
Web3 Example:
```js
let numOfReserves = await KyberNetwork.methods.getNumReserves().call();
```
<br />

### `getReserves`
Get all the reserves added in the network.
___
function __getReserves__() public view returns (KyberReserveInterface[])\
**Returns:**\
An array of all reserves' contract addresses
___
Web3 Example:
```js
let reserves = await KyberNetwork.methods.getReserves().call();
```
<br />

### `getUserCapInWei`
Get the user's exchange limit in ETH based on whether user has been KYC'd or not.
___
function __getUserCapInWei__(address user) public view returns (uint)
| Parameter | Type    | Description    |
| --------- |:-------:|:--------------:|
| `user`    | address | user's address |
**Returns:**\
The user's exchange limit in ETH wei
___
Web3 Example:
```js
const user = account.address;
let userCapInWei = await KyberNetwork.methods.getUserCapInWei(user).call()
```
<br />

### `getUserCapInTokenWei`
Get the user's exchange limit in the specified ERC20 token based on whether user has been KYC'd or not.
___
function __getUserCapInTokenWei__(address user, ERC20 token) public view returns (uint)
| Parameter | Type    | Description    |
| --------- |:-------:|:--------------:|
| `user`    | address | user's address |
| `token` | ERC20 | ERC20 token contract address |
**Returns:**\
The user's exchange limit in the specified ERC20 token wei
___
Web3 Example:
```js
const user = account.address
const token = '0xdd974D5C2e2928deA5F71b9825b8b646686BD200' // KNC
let userCapInTokenWei = await KyberNetwork.methods.getUserCapInTokenWei(user,token).call()
```
<br />

### `info`
Get the information only used by an external UI application.
___
function __info__(bytes32 field) public view returns (uint)
| Parameter | Type    | Description                         |
| --------- |:-------:|:-----------------------------------:|
| `field`   | bytes32 | key in the `info` mapping           |
**Returns:**\
Value that was mapped to the `field` input
___
Web3 Example:
```js
let value =  await KyberNetwork.methods.info(`field`).call()
```
<br />

### `listPairForReserve`
Allow or prevent a specific reserve to trade a pair of tokens. Only admin can invoke.
___
function __listPairForReserve__(address reserve, ERC20 token, bool ethToToken, bool tokenToEth, bool add) public onlyAdmin
| Parameter | Type                  | Description                                                 |
| ----------|:---------------------:|:-----------------------------------------------------------:|
| `reserve` | KyberReserveInterface | reserve's contract address                                  |
| `token` | ERC20 | Token contract address |
| `ethToToken`     | bool                 | `true` if ETH -> token trade supported, `false` otherwise      |
| `tokenToEth`     | bool                 | `true` if token -> ETH trade supported, `false` otherwise                    |
| `add`     | uint                  | `true` if trade enabled, otherwise disable trade if `false` |
Modifiers: [onlyAdmin](api-permissiongroups.md#onlyadmin)
___
Web3 Example:
```js
const reserve = '0x798AbDA6Cc246D0EDbA912092A2a3dBd3d11191B';
const token = '0xdd974D5C2e2928deA5F71b9825b8b646686BD200' // KNC
const ethToToken = true
const tokenToEth = false
const add = true

let transactionData = KyberNetwork.methods.listPairForReserve(
  reserve,
	token,
	ethToToken,
	tokenToEth,
  add
).encodeABI()

txReceipt = await web3.eth.sendTransaction({
    from: ADMIN_ADDRESS,
    to: KYBER_NETWORK_ADDRESS,
    data: transactionData
})
```
<br />

### `maxGasPrice`
Get the maximum gas price allowable for trade transactions.
___
function __maxGasPrice__() public view returns (uint)\
**Returns:**\
Maximum gas price
___
Web3 Example:
```js
let maxGasPrice = await KyberNetwork.methods.maxGasPrice().call();
```
<br />

### `searchBestRate`
Obtain the best exchange rate.\
**Note: either `src` or `dest` must be ETH, ie. srcToken -> ETH or ETH -> destToken**.
___
function __searchBestRate__(ERC20 src, ERC20 dest, uint srcAmount) public view returns (address, uint)
| Parameter | Type    | Description                         |
| --------- |:-------:|:-----------------------------------:|
| `src`   | ERC20 | source token contract address           |
| `dest`   | ERC20 | destination token contract address           |
| `srcAmount` | uint | wei amount of source ERC20 token |
**Returns:**\
Reserve address that offered the best rate, and the actual exchange rate.
___
Web3 Example:
```js
src = '0xeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeee'; // ETH
dest = '0xdd974D5C2e2928deA5F71b9825b8b646686BD200' // KNC
srcAmount = new web3.utils.BN('3000000000000000000000');

let result =  await KyberNetwork.methods.searchBestRate(src,dest,srcAmount).call()
reserveAddress = result[0]
rate = result[1]
```
<br />

### `setEnable`
Enables or disables the entire network (Eg. disabled when smart contracts are upgraded)
___
function __setEnable__(bool \_enable) public onlyAdmin
| Parameter | Type  | Description                              |
| --------- |:-----:|:----------------------------------------:|
| `_enable` | bool  | `true` to enable network, `false` to disable |
Modifiers: [onlyAdmin](api-permissiongroups.md#onlyadmin)
___
Web3 Example:
```js
const _enable = true;
let transactionData = KyberNetwork.methods.setEnable(_enable).encodeABI()

txReceipt = await web3.eth.sendTransaction({
    from: ADMIN_ADDRESS,
    to: KYBER_NETWORK_ADDRESS,
    data: transactionData
})
```
<br />

### `setExpectedRate`
Set whiteList contract address. Only admin can invoke.
___
function __setExpectedRate__(ExpectedRateInterface expectedRate) public onlyAdmin
| Parameter             | Type                  | Description                                    |
| ----------------------|:---------------------:|:----------------------------------------------:|
| `expectedRate`          | ExpectedRateInterface     | ExpectedRate contract address                     |
Modifiers: [onlyAdmin](api-permissiongroups.md#onlyadmin)
___
Web3 Example:
```js
const expectedRate = '0x543f2592b1C478837B5EE2013eD26C7Fe04a8C7e';
let transactionData = KyberNetwork.methods.setExpectedRate(expectedRate).encodeABI()

txReceipt = await web3.eth.sendTransaction({
    from: ADMIN_ADDRESS,
    to: KYBER_NETWORK_ADDRESS,
    data: transactionData
})
```
<br />

### `setFeeBurner`
Set feeBurner contract address. Only admin can invoke.
___
function __setFeeBurner__(FeeBurnerInterface  feeBurner) public onlyAdmin
| Parameter             | Type                  | Description                                    |
| ----------------------|:---------------------:|:----------------------------------------------:|
| `feeBurner`          | FeeBurnerInterface     | FeeBurner contract address                      |
Modifiers: [onlyAdmin](api-permissiongroups.md#onlyadmin)
___
Web3 Example:
```js
const feeBurner = '0x543f2592b1C478837B5EE2013eD26C7Fe04a8C7e';
let transactionData = KyberNetwork.methods.setFeeBurner(feeBurner).encodeABI()

txReceipt = await web3.eth.sendTransaction({
    from: ADMIN_ADDRESS,
    to: KYBER_NETWORK_ADDRESS,
    data: transactionData
})
```
<br />

### `setInfo`
Sets the information only used by an external UI application.
___
function __setInfo__(bytes32 field, uint value) public onlyOperator
| Parameter | Type    | Description                         |
| --------- |:-------:|:-----------------------------------:|
| `field`   | bytes32 | key in the `info` mapping           |
| `value`   | uint    | value to be set in the `info` field |
Modifiers: [onlyOperator](api-permissiongroups.md#onlyoperator)
___
Web3 Example:
```js
const field = 'someField'
const value = 'someValue'
let transactionData = KyberNetwork.methods.setInfo(field,value).encodeABI()

txReceipt = await web3.eth.sendTransaction({
    from: OPERATOR_ADDRESS,
    to: KYBER_NETWORK_ADDRESS,
    data: transactionData
})
```
<br />

### `setKyberProxy`
Sets the [`KyberNetworkProxy.sol`](api-kybernetworkproxy.md) contract address.
___
function __setKyberProxy__(address networkProxy) public onlyAdmin
| Parameter | Type    | Description                         |
| --------- |:-------:|:-----------------------------------:|
| `networkProxy`   | address | KyberNetworkProxy contract address |
Modifiers: [onlyAdmin](api-permissiongroups.md#onlyadmin)
___
Web3 Example:
```js
const networkProxy = '0x818E6FECD516Ecc3849DAf6845e3EC868087B755'
let transactionData = KyberNetwork.methods.setKyberProxy(networkProxy).encodeABI()

txReceipt = await web3.eth.sendTransaction({
    from: ADMIN_ADDRESS,
    to: KYBER_NETWORK_ADDRESS,
    data: transactionData
})
```
<br />

### `setParams`
Sets the parameters for `maxGasPrice` and `negligibleRateDiff`. Only admin can invoke.
___
function __setParams__(uint \_maxGasPrice, uint \_negligibleRateDiff) public onlyAdmin
| Parameter             | Type                  | Description                                    |
| ----------------------|:---------------------:|:----------------------------------------------:|
| `_maxGasPrice`        | uint                  | maximum gas price for trades |
| `_negligibleRateDiff` | uint                  | Neligible rate difference         |
Modifiers: [onlyAdmin](api-permissiongroups.md#onlyadmin)
___
Web3 Example:
```js
const _maxGasPrice = new web3.utils.BN('50000000000');
const _negligibleRateDiff = new web3.utils.BN('350000000');

let transactionData = KyberNetwork.methods.setParams(_maxGasPrice, _negligibleRateDiff).encodeABI()

txReceipt = await web3.eth.sendTransaction({
    from: ADMIN_ADDRESS,
    to: KYBER_NETWORK_ADDRESS,
    data: transactionData
})
```
<br />

### `setWhiteList`
Set whiteList contract address. Only admin can invoke.
___
function __setWhiteList__(WhiteListInterface whiteList) public onlyAdmin
| Parameter             | Type                  | Description                                    |
| ----------------------|:---------------------:|:----------------------------------------------:|
| `whiteList`          | WhiteListInterface    | WhiteList contract address                     |
Modifiers: [onlyAdmin](api-permissiongroups.md#onlyadmin)
___
Web3 Example:
```js
const whiteList = '0x543f2592b1C478837B5EE2013eD26C7Fe04a8C7e';
let transactionData = KyberNetwork.methods.setWhiteList(whiteList).encodeABI()

txReceipt = await web3.eth.sendTransaction({
    from: ADMIN_ADDRESS,
    to: KYBER_NETWORK_ADDRESS,
    data: transactionData
})
```