# JS API

## Global Objects

### console

The `console` module provides a simple debugging console that is similar to the JavaScript console mechanism provided by web browsers.

The global console can be used without calling `require('console')`.

#### console.info\(\[...args\]\)

* `...args <any>`

  The console.info\(\) function is an alias for `console.log()`.

#### console.log\(\[...args\]\)

* `...args <any>`

  Print `args` to Nebulas Logger at level `info`.

#### console.debug\(\[...args\]\)

* `...args <any>`

  Print `args` to Nebulas Logger at level `debug`.

#### console.warn\(\[...args\]\)

* `...args <any>`

  Print `args` to Nebulas Logger at level `warn`.

#### console.error\(\[...args\]\)

* `...args <any>`

  Print `args` to Nebulas Logger at level `error`.

### LocalContractStorage

The `LocalContractStorage` module provides a state trie based storage capability. It accepts string only key value pairs. And all data are stored to a private state trie associated with current contract address, only the contract can access them.

```typescript
interface Descriptor {
    // serialize value to string;
    stringify?(value: any): string;

    // deserialize value from string;
    parse?(value: string): any;
}

interface DescriptorMap {
    [fieldName: string]: Descriptor;
}

interface ContractStorage {
    // get and return value by key from Native Storage.
    rawGet(key: string): string;
    // set key and value pair to Native Storage,
    // return 0 for success, otherwise failure.
    rawSet(key: string, value: string): number;

    // define a object property named `fieldname` to `obj` with descriptor.
    // default descriptor is JSON.parse/JSON.stringify descriptor.
    // return this.
    defineProperty(obj: any, fieldName: string, descriptor?: Descriptor): any;

    // define object properties to `obj` from `props`.
    // default descriptor is JSON.parse/JSON.stringify descriptor.
    // return this.
    defineProperties(obj: any, props: DescriptorMap): any;

    // define a StorageMap property named `fieldname` to `obj` with descriptor.
    // default descriptor is JSON.parse/JSON.stringify descriptor.
    // return this.
    defineMapProperty(obj: any, fieldName: string, descriptor?: Descriptor): any;

    // define StorageMap properties to `obj` from `props`.
    // default descriptor is JSON.parse/JSON.stringify descriptor.
    // return this.
    defineMapProperties(obj: any, props: DescriptorMap): any;

    // delete key from Native Storage.
    // return 0 for success, otherwise failure.
    del(key: string): number;

    // get value by key from Native Storage,
    // deserialize value by calling `descriptor.parse` and return.
    get(key: string): any;

    // set key and value pair to Native Storage,
    // the value will be serialized to string by calling `descriptor.stringify`.
    // return 0 for success, otherwise failure.
    set(key: string, value: any): number;
}

interface StorageMap {
    // delete key from Native Storage, return 0 for success, otherwise failure.
    del(key: string): number;

    // get value by key from Native Storage,
    // deserialize value by calling `descriptor.parse` and return.
    get(key: string): any;

    // set key and value pair to Native Storage,
    // the value will be serialized to string by calling `descriptor.stringify`.
    // return 0 for success, otherwise failure.
    set(key: string, value: any): number;
}
```

### BigNumber

The `BigNumber` module use the [bignumber.js](https://github.com/MikeMcl/bignumber.js), a JavaScript library for arbitrary-precision decimal and non-decimal arithmetic. The contract can use `BigNumber` directly to handle the value of the transaction and other values transfer.

```javascript
var value = new BigNumber(0);
value.plus(1);
...
```

### Blockchain

The `Blockchain` module provides a object for contracts to obtain transactions and blocks executed by the current contract. Also, the NAS can be transferred from the contract and the address check is provided.

Blockchain API:

```javascript
// current block 
Blockchain.block;

// current transaction, transaction's value/gasPrice/gasLimit auto change to BigNumber object
Blockchain.transaction;

// transfer NAS from contract to address
Blockchain.transfer(address, value);

// verify address
Blockchain.verifyAddress(address);
```

properties:

* `block`: current block for contract execution
  * `timestamp`: block timestamp
  * `hash`: block hash
  * `height`: block height
* `transaction`: current transaction for contract execution
  * `hash`: transaction hash
  * `from`: transaction from address
  * `to`: transaction to address
  * `value`: transaction value, a BigNumber object for contract use
  * `nonce`: transaction nonce
  * `timestamp`: transaction timestamp
  * `gasPrice`: transaction gasPrice, a BigNumber object for contract use
  * `gasLimit`: transaction gasLimit, a BigNumber object for contract use
* `transfer(address, value)`: transfer NAS from contract to address
  * params:
    * `address`: nebulas address to receive NAS
    * `value`: transfer value, a BigNumber object
  * return:
    * `0`: transfer success
    * `1`: transfer failed   
* `verifyAddress(address)`: verify address
  * params:
    * `address`: address need to check
  * return:
    * `1`: address is valid
    * `0`: address is invalid 

Example to use:

```javascript
'use strict';

var SampleContract = function () {
    LocalContractStorage.defineProperties(this, {
        name: null,
        count: null
    });
    LocalContractStorage.defineMapProperty(this, "allocation");
};

SampleContract.prototype = {
    init: function (name, count, allocation) {
        this.name = name;
        this.count = count;
        allocation.forEach(function (item) {
            this.allocation.put(item.name, item.count);
        }, this);
        console.log('init: Blockchain.block.coinbase = ' + Blockchain.block.coinbase);
        console.log('init: Blockchain.block.hash = ' + Blockchain.block.hash);
        console.log('init: Blockchain.block.height = ' + Blockchain.block.height);
        console.log('init: Blockchain.transaction.from = ' + Blockchain.transaction.from);
        console.log('init: Blockchain.transaction.to = ' + Blockchain.transaction.to);
        console.log('init: Blockchain.transaction.value = ' + Blockchain.transaction.value);
        console.log('init: Blockchain.transaction.nonce = ' + Blockchain.transaction.nonce);
        console.log('init: Blockchain.transaction.hash = ' + Blockchain.transaction.hash);
    },
    transfer: function (address, value) {
        var result = Blockchain.transfer(address, value);
        console.log("transfer result:", result);
        Event.Trigger("transfer", {
            Transfer: {
                from: Blockchain.transaction.to,
                to: address,
                value: value
            }
        });
    },
    verifyAddress: function (address) {
         var result = Blockchain.verifyAddress(address);
        console.log("verifyAddress result:", result);
    }
};

module.exports = SampleContract;
```

### Event

The `Event` module records execution events in contract. The recorded events are stored in the event trie on the chain, which can be fetched by `FetchEvents` method in block with the execution transaction hash. All contract event topics have a `chain.contract.` prefix before the topic they set in contract.

```javascript
Event.Trigger(topic, obj);
```

* `topic`: user-defined topic
* `obj`: JSON object

You can see the example in `SampleContract` before.

