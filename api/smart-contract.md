# Smart Contract

## Languages

In Nebulas, there are two supported smart contract languages:

* [JavaScript](https://en.wikipedia.org/wiki/JavaScript)
* [TypeScript](https://en.wikipedia.org/wiki/TypeScript)

They are supported by the integration of [Chrome V8](https://developers.google.com/v8/), a widely used JavaScript engine developed by The Chromium Project for Google Chrome and Chromium web browsers.

## Execution Model

The diagram below is the Execution Model of Smart Contract:

![Smart Contract Execution Model](https://github.com/dreamflyfengzi/nebulasdapp/tree/c1613497824d4e0d9fa0bf9d6bde1c719f742fe6/api/resources/smart_contract_execution_model.png)

1. All src of Smart Contract and arguments are packaged in Transaction and deployed on Nebulas.
2. The execution of Smart Contract are divided into two phases:
   1. Preprocess: inject tracing instruction, etc.
   2. Execute: generate executable src and execute it.

## Contracts

Contracts in Nebulas are similar to classes in object-oriented languages. They contain persistent data in state variables and functions that can modify these variables.

### Writing Contract

A contract must be a Prototype Object or Class in JavaScript or TypeScript.

A Contract must include an `init` function, it will be executed only once when deploying. Functions, named starting with `_` are `private`, can't be executed in Transaction. The others are all `public` and can be executed in Transaction.

Since Contract is executed in Chrome V8, all instance variables are in memory, it's not wise to save all of them to [state trie](https://github.com/nebulasio/wiki/blob/master/merkle_trie.md) in Nebulas. In Nebulas, we provide `LocalContractStorage` and `GlobalContractStorage` objects to help developers define fields needing to be saved to state trie. And those fields should be defined in `constructor` of Contract, before other functions.

The following is a sample contract:

```javascript
class Rectangle {
    constructor() {
        // define fields stored to state trie.
        LocalContractStorage.defineProperties(this, {
            height: null,
            width: null,
        });
    }

    // init function.
    init(height, width) {
        this.height = height;
        this.width = width;
    }

    // calc area function.
    calcArea() {
        return this.height * this.width;
    }

    // verify function.
    verify(expected) {
        let area = this.calcArea();
        if (expected != area) {
            throw new Error("Error: expected " + expected + ", actual is " + area + ".");
        }
    }
}
```

### Visibility

In JavaScript, there is no function visibility, all functions defined in prototype object are public.

In Nebulas, we define two kinds of visibility `public` and `private`:

* `public` All functions whose name matches regexp `^[a-zA-Z$][A-Za-z0-9_$]*$` are public, except `init`. Public functions can be called via Transaction.
* `private` All functions whose name starts with `_` are private. A private function can only be called by public functions.

### Web SDK

[https://github.com/nebulasio/neb.js](https://github.com/nebulasio/neb.js)

