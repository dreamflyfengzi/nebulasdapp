# 你的第一个app

## 编写智能合约

跟以太坊类似，Nebulas实现了NVM虚拟机来运行智能合约，NVM的实现使用了JavaScript V8引擎，所以当前的开发版，我们可以使用JavaScript、TypeScript来编写智能合约。

编写智能合约的简要规范：

1. 智能合约代码必须是一个Prototype的对象；
2. 智能合约代码必须有一个init\(\)的方法，这个方法只会在部署的时候被执行一次；
3. 智能合约里面的私有方法是以\_开头的方法，私有方法不能被外部直接调用；

下面我们使用JavaScript来编写第一个智能合约：银行保险柜。 这个智能合约需要实现以下功能：

1. 用户可以向这个银行保险柜存钱。
2. 用户可以从这个银行保险柜取钱。
3. 用户可以查询银行保险柜中的余额。

智能合约示例：

```javascript
"use strict";

var DepositeContent = function (text) {
     if (text) {
      var o = JSON.parse(text);
      this.balance = new BigNumber(o.balance);
      this.expiryHeight = new BigNumber(o.expiryHeight);
   } else {
      this.balance = new BigNumber(0);
      this.expiryHeight = new BigNumber(0);
   }
};

DepositeContent.prototype = {
   toString: function () {
      return JSON.stringify(this);
   }
};

var BankVaultContract = function () {
   LocalContractStorage.defineMapProperty(this, "bankVault", {
      parse: function (text) {
         return new DepositeContent(text);
      },
      stringify: function (o) {
         return o.toString();
      }
   });
};

// save value to contract, only after height of block, users can takeout
BankVaultContract.prototype = {
   init: function () {
      //TODO:
   },

   save: function (height) {
      var from = Blockchain.transaction.from;
      var value = Blockchain.transaction.value;
      var bk_height = new BigNumber(Blockchain.block.height);

      var orig_deposit = this.bankVault.get(from);
      if (orig_deposit) {
         value = value.plus(orig_deposit.balance);
      }

      var deposit = new DepositeContent();
      deposit.balance = value;
      deposit.expiryHeight = bk_height.plus(height);

      this.bankVault.put(from, deposit);
   },

   takeout: function (value) {
      var from = Blockchain.transaction.from;
      var bk_height = new BigNumber(Blockchain.block.height);
      var amount = new BigNumber(value);

      var deposit = this.bankVault.get(from);
      if (!deposit) {
         throw new Error("No deposit before.");
      }

      if (bk_height.lt(deposit.expiryHeight)) {
         throw new Error("Can not takeout before expiryHeight.");
      }

      if (amount.gt(deposit.balance)) {
         throw new Error("Insufficient balance.");
      }

      var result = Blockchain.transfer(from, amount);
      if (!result) {
         throw new Error("transfer failed.");
      }
      Event.Trigger("BankVault", {
         Transfer: {
            from: Blockchain.transaction.to,
            to: from,
            value: amount.toString()
         }
      });

      deposit.balance = deposit.balance.sub(amount);
      this.bankVault.put(from, deposit);
   },

   balanceOf: function () {
      var from = Blockchain.transaction.from;
      return this.bankVault.get(from);
   },

   verifyAddress: function (address) {
      // 1-valid, 0-invalid
      var result = Blockchain.verifyAddress(address);
      return {
         valid: result == 0 ? false : true
      };
   }
};

module.exports = BankVaultContract;
```

