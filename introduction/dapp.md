# Dapp

## Languages

In Nebulas, there are two supported smart contract languages:

* [JavaScript](https://en.wikipedia.org/wiki/JavaScript)
* [TypeScript](https://en.wikipedia.org/wiki/TypeScript)

They are supported by the integration of [Chrome V8](https://developers.google.com/v8/), a widely used JavaScript engine developed by The Chromium Project for Google Chrome and Chromium web browsers.

## Execution Model

The diagram below is the Execution Model of Smart Contract:

![Smart Contract Execution Model](https://github.com/dreamflyfengzi/nebulasdapp/tree/63ca4423ba3208505ecfa9e13ba0425936e06f56/introduction/resources/smart_contract_execution_model.png)

1. All src of Smart Contract and arguments are packaged in Transaction and deployed on Nebulas.
2. The execution of Smart Contract are divided into two phases:
   1. Preprocess: inject tracing instruction, etc.
   2. Execute: generate executable src and execute it.

## Contracts

Contracts in Nebulas are similar to classes in object-oriented languages. They contain persistent data in state variables and functions that can modify these variables.

