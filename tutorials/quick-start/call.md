# 调用

## 执行智能合约方法

在Nebulas中调用智能合约的方式也很简单，同样是通过发送交易来调用智能合约。

### 调用智能合约的`save`方法

```bash
> curl -i -H 'Accept: application/json' -X POST http://localhost:8685/v1/admin/transactionWithPassphrase -H 'Content-Type: application/json' -d '{"transaction":{"from":"n1LkDi2gGMqPrjYcczUiweyP4RxTB6Go1qS","to":"n1rVLTRxQEXscTgThmbTnn2NqdWFEKwpYUM", "value":"100","nonce":1,"gasPrice":"1000000","gasLimit":"2000000","contract":{"function":"save","args":"[0]"}}, "passphrase": "passphrase"}'

{"result":{"txhash":"5337f1051198b8ac57033fec98c7a55e8a001dbd293021ae92564d7528de3f84","contract_address":""}}
```

* from: 用户的账户地址
* to: 智能合约地址
* value: 调用save\(\)方法时想要存入智能合约代币数量
* nonce: 比创建者当前的`nonce`多1，可以通过[`GetAccountState`](https://github.com/nebulasio/wiki/blob/master/rpc.md#getaccountstate)获取创建前当前`nonce`
* gasPrice：部署智能合约用到的gasPrice，可以通过[`GetGasPrice`](https://github.com/nebulasio/wiki/blob/master/rpc.md#getgasprice)获取，或者使用默认值:`"1000000"`；
* gasLimit: 部署合约的gasLimit，通过[`EstimateGas`](https://github.com/nebulasio/wiki/blob/master/rpc.md#estimateGas)可以获取部署合约的gas消耗，不能使用默认值，也可以设置一个较大值，执行时以实际使用计算。
* contract: 合约信息，部署合约时传入的参数
  * `function`: 调用合约方法
  * `args`: 合约方法参数，无参数为空字符串，有参数时为JSON数组

> **验证智能合约的方法**`save`**是否执行成功**
>
> 由于执行合约方法本质是提交一个交易，所以我们可以通过验证交易是否提交成功来判断合约方法是否执行成功。
>
> ```bash
> > curl -i -H 'Content-Type: application/json' -X POST http://localhost:8685/v1/user/getTransactionReceipt -d '{"hash":"5337f1051198b8ac57033fec98c7a55e8a001dbd293021ae92564d7528de3f84"}'
>
> {"result":{"hash":"5337f1051198b8ac57033fec98c7a55e8a001dbd293021ae92564d7528de3f84","chainId":100,"from":"n1LkDi2gGMqPrjYcczUiweyP4RxTB6Go1qS","to":"n1rVLTRxQEXscTgThmbTnn2NqdWFEKwpYUM","value":"100","nonce":"1","timestamp":"1524712532","type":"call","data":"eyJGdW5jdGlvbiI6InNhdmUiLCJBcmdzIjoiWzBdIn0=","gas_price":"1000000","gas_limit":"2000000","contract_address":"","status":1,"gas_used":"20361"}}
> ```
>
> 如上所示，交易状态变为了1，表示执行`save`方法成功了。

### 调用智能合约的`takeout`方法

```bash
> curl -i -H 'Accept: application/json' -X POST http://localhost:8685/v1/admin/transactionWithPassphrase -H 'Content-Type: application/json' -d '{"transaction":{"from":"n1LkDi2gGMqPrjYcczUiweyP4RxTB6Go1qS","to":"n1rVLTRxQEXscTgThmbTnn2NqdWFEKwpYUM", "value":"0","nonce":2,"gasPrice":"1000000","gasLimit":"2000000","contract":{"function":"takeout","args":"[50]"}}, "passphrase": "passphrase"}'

{"result":{"txhash":"46a307e9beb21f52992a7512f3705fe58ee6c1887122a1b52f5ce5fd5f536a91","contract_address":""}}
```

> **验证智能合约的方法**`takeout`**是否执行成功**
>
> 在上面save方法的执行中，我们在合约`n1rVLTRxQEXscTgThmbTnn2NqdWFEKwpYUM`中存了100NAS。此时，我们执行`takeout`函数，从中取出50NAS。合约里应该还有50NAS。我们检测下合约账户的余额来验证`takeout`方法执行是否成功。
>
> ```bash
> > curl -i -H 'Content-Type: application/json' -X POST http://localhost:8685/v1/user/accountstate -d '{"address":"n1rVLTRxQEXscTgThmbTnn2NqdWFEKwpYUM"}'
>
> {"result":{"balance":"50","nonce":"0","type":88}}
> ```
>
> 结果和预期相符。

## 查询智能合约数据

在智能合约中，我们有一些方法并不更改合约的状态，这些方法被设计来帮助我们获取合约数据，它们是只读的。在星云链上，我们在API Module中为用户提供了[`Call`](https://github.com/nebulasio/wiki/blob/master/rpc.md#call)接口来帮助用户来执行这些只读的方法，使用`Call`接口不会发送交易，也就无需支付上链手续费。

我们调用合约`n1rVLTRxQEXscTgThmbTnn2NqdWFEKwpYUM`中的`balanceOf`方法来查询该合约的余额。

```bash
> curl -i -H 'Accept: application/json' -X POST http://localhost:8685/v1/user/call -H 'Content-Type: application/json' -d '{"from":"n1LkDi2gGMqPrjYcczUiweyP4RxTB6Go1qS","to":"n1rVLTRxQEXscTgThmbTnn2NqdWFEKwpYUM","value":"0","nonce":3,"gasPrice":"1000000","gasLimit":"2000000","contract":{"function":"balanceOf","args":""}}'

{"result":{"result":"{\"balance\":\"50\",\"expiryHeight\":\"84\"}","execute_err":"","estimate_gas":"20209"}}
```

