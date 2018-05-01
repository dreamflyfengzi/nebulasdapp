# 主网

## Introduction

We are glad to release Nebulas Mainnet here. Please join and enjoy Nebulas Mainnet.

```text
https://github.com/nebulasio/go-nebulas/tree/master
```

### Configuration

The Mainnet configuration files are in folder [`mainnet/conf`](https://github.com/nebulasio/go-nebulas/tree/master/mainnet/conf), including

### genesis.conf

All configurable information about genesis block is defined in genesis.conf, including

* **meta.chain\_id:** chain identity
* **consensus.dpos.dynasty:** the initial dynasty of validators
* **token\_distribution:** the initial allocation of tokens

> _Attention_: DO NOT change the genesis.conf.

### config.conf

All configurable information about runtime is defined in config.conf.

Please check the [`template.conf`](https://github.com/dreamflyfengzi/nebulasdapp/tree/c1613497824d4e0d9fa0bf9d6bde1c719f742fe6/introduction/resources/conf/template.conf) to find more details about the runtime configuration.

> _Tips_: the official seed node info is as below,

```javascript
seed:["/ip4/52.2.205.12/tcp/8680/ipfs/QmQK7W8wrByJ6So7rf84sZzKBxMYmc1i4a7JZsne93ysz5","/ip4/52.56.55.238/tcp/8680/ipfs/QmVy9AHxBpd1iTvECDR7fvdZnqXeDhnxkZJrKsyuHNYKAh","/ip4/13.251.33.39/tcp/8680/ipfs/QmVm5CECJdPAHmzJWN2X7tP335L5LguGb9QLQ78riA9gw3"]
```

