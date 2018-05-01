# 测试网

## Introduction

We are glad to release Nebulas Testnet here. It simulate the Nebulas network and NVM, and allow developers to interact with Nebulas without paying the cost of gas.

```text
https://github.com/nebulasio/go-nebulas/tree/testnet
```

### Configuration

The testnet configuration files are in folder [`testnet/conf`](https://github.com/nebulasio/go-nebulas/tree/testnet/testnet/conf) under `testnet` branch, including

#### genesis.conf

All configurable information about genesis block is defined in genesis.conf, including

* **meta.chain\_id:** chain identity
* **consensus.dpos.dynasty:** the initial dynasty of validators
* **token\_distribution:** the initial allocation of tokens

> _Attention_: DO NOT change the genesis.conf.

#### config.conf

All configurable information about runtime is defined in config.conf.

Please check the [`template.conf`](https://github.com/dreamflyfengzi/nebulasdapp/tree/c1613497824d4e0d9fa0bf9d6bde1c719f742fe6/introduction/resources/conf/template.conf) to find more details about the runtime configuration.

> _Tips_: the official seed node info is as below,

```javascript
seed:["/ip4/52.60.150.236/tcp/8680/ipfs/QmVJikqWQst13QsgdCLBjgcSWwpAAdZjoExGdvK3r2CNhv"]
```


#### Token Claim

Every email can claim some tokens every day at [here](https://testnet.nebulas.io/claim).
