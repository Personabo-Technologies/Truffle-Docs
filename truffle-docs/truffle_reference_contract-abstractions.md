<div>

::: {md-component="content"}
# Contract Abstractions[¶](#contract-abstractions "Permanent link")

Truffle provides contract abstractions for interacting with your
contracts. Skip ahead to the [api
section](/docs/truffle/reference/contract-abstractions#api) for a list
of contract methods.

### Usage[¶](#usage "Permanent link")

To obtain a contract abstraction you can require it with the contract
name from the `artifacts` object. Outside of the console this is an
object available in migration files, tests, and exec scripts. You would
require your contract as follows:

<div>

``` {#__code_1}
const MyContract = artifacts.require("MyContract");
```

</div>

You can also obtain one in the developer console. Your contract types
are available here and all you need to do is use the `at`, `deployed`,
or `new` method.

<div>

    truffle(development)> const myContract = await MyContract.deployed();

</div>

You now have access to the following functions on `MyContract`, as well
as many others:

-   `at()`: Create an instance of `MyContract` that represents your
    contract at a specific address.
-   `deployed()`: Create an instance of `MyContract` that represents the
    default address managed by `MyContract`.
-   `new()`: Deploy a new version of this contract to the network,
    getting an instance of `MyContract` that represents the newly
    deployed instance.

Each instance is tied to a specific address on the Ethereum network, and
each instance has a 1-to-1 mapping from Javascript functions to contract
functions. For instance, if your Solidity contract had a function
defined `someFunction(uint value) {}` (solidity), then you could execute
that function on the network like so:

<div>

    let deployed;
    MyContract.deployed()
      .then((instance) => {
        deployed = instance;
        return deployed.someFunction(5);
      }).then((result) => {
        // Do something with the result or continue with more transactions.
      });

</div>

You can also use async/await syntax which is often much less verbose. We
will use async/await for the rest of this document but you may also use
a promises for interfacing with contract methods as well.

<div>

    const deployed = await MyContract.deployed();
    const result = await deployed.someFunction(5);
    // Do something with the result or continue with more transactions.

</div>

See the [processing transaction
results](/docs/truffle/getting-started/interacting-with-your-contracts#processing-transaction-results)
section to learn more about the results object obtained from making
transactions.

Contract methods and events have an EventEmitter interface. So you can
set up handlers like the following:

<div>

    const example = await artifacts.require("Example").deployed();

    example
      .setValue(45)
      .on('transactionHash', hash => {})
      .on('receipt', receipt => {})
      .on('error', error => {})
      .on('confirmation', (num, receipt) => {})
      .then(receipt => {});

</div>

<div>

    example
      .ExampleEvent()
      .on('data', event => ... etc ... )

    example
      .ExampleEvent()
      .once('data', event => ... etc ... )

</div>

## API[¶](#api "Permanent link")

There are two API\'s you\'ll need to be aware of. One is the static
Contract Abstraction API and the other is the Contract Instance API. The
Abstraction API is a set of functions that exist for all contract
abstractions, and those function exist on the abstraction itself (i.e.,
`MyContract.at()`). In contrast, the Instance API is the API available
to contract instances \-- i.e., abstractions that represent a specific
contract on the network \-- and that API is created dynamically based on
functions available in your Solidity source file.

### Contract Abstraction API[¶](#contract-abstraction-api "Permanent link")

Each contract abstraction \-- `MyContract` in the examples above \--
have the following useful functions:

#### `MyContract.new([arg1, arg2, ...], [tx params])`[¶](#mycontractnewarg1-arg2-tx-params "Permanent link") {#mycontractnewarg1-arg2-tx-params}

This function takes whatever constructor parameters your contract
requires and deploys a new instance of the contract to the network.
There\'s an optional last argument which you can use to pass transaction
parameters including the transaction from address, gas limit and gas
price. This function returns a Promise that resolves into a new instance
of the contract abstraction at the newly deployed address.

#### `MyContract.at(address)`[¶](#mycontractataddress "Permanent link") {#mycontractataddress}

This function creates a new instance of the contract abstraction
representing the contract at the passed in address. Returns a
\"thenable\" object (not yet an actual Promise for backward
compatibility). Resolves to a contract abstraction instance after
ensuring code exists at the specified address.

#### `MyContract.deployed()`[¶](#mycontractdeployed "Permanent link") {#mycontractdeployed}

Creates an instance of the contract abstraction representing the
contract at its deployed address. The deployed address is a special
value given to \@truffle/contract that, when set, saves the address
internally so that the deployed address can be inferred from the given
Ethereum network being used. This allows you to write code referring to
a specific deployed contract without having to manage those addresses
yourself. Like `at()`, `deployed()` is thenable, and will resolve to a
contract abstraction instance representing the deployed contract after
ensuring that code exists at that location and that that address exists
on the network being used.

#### `MyContract.link(instance)`[¶](#mycontractlinkinstance "Permanent link") {#mycontractlinkinstance}

Link a library represented by a contract abstraction instance to
MyContract. The library must first be deployed and have its deployed
address set. The name and deployed address will be inferred from the
contract abstraction instance. When this form of `MyContract.link()` is
used, MyContract will consume all of the linked library\'s events and
will be able to report that those events occurred during the result of a
transaction.

Libraries can be linked multiple times and will overwrite their previous
linkage.

Note: This method has two other forms, but this form is recommended.

#### `MyContract.link(name, address)`[¶](#mycontractlinkname-address "Permanent link") {#mycontractlinkname-address}

Link a library with a specific name and address to MyContract. The
library\'s events will not be consumed using this form.

#### `MyContract.link(object)`[¶](#mycontractlinkobject "Permanent link") {#mycontractlinkobject}

Link multiple libraries denoted by an Object to MyContract. The keys
must be strings representing the library names and the values must be
strings representing the addresses. Like above, libraries\' events will
not be consumed using this form.

#### `MyContract.networks()`[¶](#mycontractnetworks "Permanent link") {#mycontractnetworks}

View a list of network ids this contract abstraction has been set up to
represent.

#### `MyContract.setProvider(provider)`[¶](#mycontractsetproviderprovider "Permanent link") {#mycontractsetproviderprovider}

Sets the web3 provider this contract abstraction will use to make
transactions.

#### `MyContract.setNetwork(network_id)`[¶](#mycontractsetnetworknetwork_id "Permanent link") {#mycontractsetnetworknetwork_id}

Sets the network that MyContract is currently representing.

#### `MyContract.hasNetwork(network_id)`[¶](#mycontracthasnetworknetwork_id "Permanent link") {#mycontracthasnetworknetwork_id}

Returns a boolean denoting whether or not this contract abstraction is
set up to represent a specific network.

#### `MyContract.defaults([new_defaults])`[¶](#mycontractdefaultsnew_defaults "Permanent link") {#mycontractdefaultsnew_defaults}

Get\'s and optionally sets transaction defaults for all instances
created from this abstraction. If called without any parameters it will
simply return an Object representing current defaults. If an Object is
passed, this will set new defaults. Example default transaction values
that can be set are:

<div>

    MyContract.defaults({
      from: ...,
      gas: ...,
      gasPrice: ...,
      value: ...
    })

</div>

Setting a default `from` address, for instance, is useful when you have
a contract abstraction you intend to represent one user (i.e., one
address).

#### `MyContract.clone(network_id)`[¶](#mycontractclonenetwork_id "Permanent link") {#mycontractclonenetwork_id}

Clone a contract abstraction to get another object that manages the same
contract artifacts, but using a different `network_id`. This is useful
if you\'d like to manage the same contract but on a different network.
When using this function, don\'t forget to set the correct provider
afterward.

<div>

    const MyOtherContract = MyContract.clone(1337);

</div>

#### `MyContract.numberFormat = number_type`[¶](#mycontractnumberformat-number_type "Permanent link") {#mycontractnumberformat-number_type}

You can set this property to choose the number format that abstraction
methods return. The default behavior is to return BN.

<div>

    // Choices are:  `["BigNumber", "BN", "String"].
    const Example = artifacts.require('Example');
    Example.numberFormat = 'BigNumber';

</div>

#### `MyContract.timeout(block_timeout)`[¶](#mycontracttimeoutblock_timeout "Permanent link") {#mycontracttimeoutblock_timeout}

This method allows you to set the block timeout for transactions.
Contract instances created from this abstraction will have the specified
transaction block timeout. This means that if a transaction does not
immediately get mined, it will retry for the specified number of blocks.

#### `MyContract.autoGas = <boolean>`[¶](#mycontractautogas-boolean "Permanent link") {#mycontractautogas-boolean}

If this is set to true, instances created from this abstraction will use
`web3.eth.estimateGas` and then apply a gas multiplier to determine the
amount of gas to include with the transaction. The default value for
this is `true`. See
[gasMultiplier](/docs/truffle/reference/contract-abstractions#mycontractgasmultipliergas_multiplier).

#### `MyContract.gasMultiplier(gas_multiplier)`[¶](#mycontractgasmultipliergas_multiplier "Permanent link") {#mycontractgasmultipliergas_multiplier}

This is the value used when `autoGas` is enabled to determine the amount
of gas to include with transactions. The gas is computed by using
`web3.eth.estimateGas` and multiplying it by the gas multiplier. The
default value is `1.25`.

### Contract Instance API[¶](#contract-instance-api "Permanent link")

Each contract instance is different based on the source Solidity
contract, and the API is created dynamically. For the purposes of this
documentation, let\'s use the following Solidity source code below:

<div>

    contract MyContract {
      uint public value;
      event ValueSet(uint val);
      function setValue(uint val) {
        value = val;
        emit ValueSet(value);
      }
      function getValue() view returns (uint) {
        return value;
      }
    }

</div>

From Javascript\'s point of view, this contract has three functions:
`setValue`, `getValue` and `value`. This is because `value` is public
and automatically creates a getter function for it.

#### Making a transaction via a contract function[¶](#making-a-transaction-via-a-contract-function "Permanent link")

When we call `setValue()`, this creates a transaction. From Javascript:

<div>

    const result = await instance.setValue(5);
    // result object contains import information about the transaction
    console.log("Value was set to", result.logs[0].args.val);

</div>

The result object that gets returned looks like this:

<div>

    {
      tx: "0x6cb0bbb6466b342ed7bc4a9816f1da8b92db1ccf197c3f91914fc2c721072ebd",
      receipt: {
        // The return value from web3.eth.getTransactionReceipt(hash)
        // See https://github.com/ethereum/wiki/wiki/JavaScript-API#web3ethgettransactionreceipt
      },
      logs: [
        { logIndex: 0,
          transactionIndex: 0,
          transactionHash: '0x728b4d1983cd00d93ae00b7adf76f78c1b32d922de636ead42e93f70cf58cdc9',
          blockHash: '0xdce5e6c580267c9bf1d82bf0a167fa60509ef9fc520b8619d8183a8373a42035',
          blockNumber: 19,
          address: '0x035b8A9e427d93D178E2D22d600B779717696831',
          type: 'mined',
          id: 'log_70be22b0',
          event: 'Transfer',
          args:
            Result {
              '0': '0x7FEb9FAA5aED0FD547Ccc70f00C19dDe95ea54d4',
              '1': '0x7FEb9FAA5aED0FD547Ccc70f00C19dDe95ea54d4',
              '2': <BN: 1>,
              __length__: 3,
              _from: '0x7FEb9FAA5aED0FD547Ccc70f00C19dDe95ea54d4',
              _to: '0x7FEb9FAA5aED0FD547Ccc70f00C19dDe95ea54d4',
              _value: <BN: 1>
            }
        }
      ],
    }

</div>

Note that if the function being executed in the transaction has a return
value, you will not get that return value inside this result. You must
instead use an event (like `ValueSet`) and look up the result in the
`logs` array.

#### Explicitly making a call instead of a transaction[¶](#explicitly-making-a-call-instead-of-a-transaction "Permanent link")

We can call `setValue()` without creating a transaction by explicitly
using `.call`:

<div>

    const value = await instance.setValue.call(5);

</div>

This isn\'t very useful in this case, since `setValue()` sets things,
and the value we pass won\'t be saved since we\'re not creating a
transaction.

#### Calling getters[¶](#calling-getters "Permanent link")

However, we can *get* the value using `getValue()`, using `.call()`.
Calls are always free and don\'t cost any Ether, so they\'re good for
calling functions that read data off the blockchain:

<div>

    const value = await instance.getValue.call();
    // value reprsents the `value` storage object in the solidity contract
    // since the contract returns that value.

</div>

Even more helpful, however is we *don\'t even need* to use `.call` when
a function is marked as `view` or `pure` (or the deprecated `constant`),
because `@truffle/contract` will automatically know that that function
can only be interacted with via a call:

<div>

    const value = await instance.getValue();
    // val reprsents the `value` storage object in the solidity contract
    // since the contract returns that value.

</div>

#### Overloaded functions[¶](#overloaded-functions "Permanent link")

Solidity supports a contract having multiple functions with the same
name as long as the function argument types differ. And so you may find
yourself in the situation of having a Truffle contract object with this
property. You can invoke these \"overloaded\" functions just like you
would a normal contract function. Truffle contract instances actually
wrap web3\'s contract abstraction (`web3.eth.Contract`) and when you
call an overloaded function, it uses the same function resolution that
web3 uses. However, we must give a warning that this overloaded function
resolution is often imprecise and can resolve to the wrong function when
you call it.

Consider the following contrived code sample:

<div>

    contract MyContract {
      uint256 public myUint;
      string public myString;
      string public myOtherString;
      function setValues(uint256 val, string str) {
        myUint = val;
        myString = str
      }
      function setValues(string str, string otherStr) {
        myString = str;
        myOtherString = otherStr;
      }
      function setValues(uint256 val, string str, string otherStr) {
        myUint = val;
        myString = str;
        myOtherString = otherStr;
      }
    }

</div>

This Solidity contract contains three functions named `setValues`, each
taking a different type of input. In your JavaScript you might do
something like the following:

<div>

    await instance.setValue(
      666,
      "this is not string cheese",
      "this is string cheese"
    );

</div>

As mentioned above, Truffle contract wraps web3 `Contract` objects and
uses web3\'s overloaded function resolution. To resolve these functions,
web3\'s strategy is to look at the number of arguments that the function
was invoked with and try one that takes that many arguments. This works
for some cases but is not ideal in that it can invoke the incorrect
function. Take, for example, the case where we do something like the
following:

<div>

    await instance.setValue("this is not string cheese", "this is string cheese");

</div>

We know that in our contract there are two functions named `setValues`
that take two arguments. In the above case, we cannot be sure whether
web3\'s resolution will resolve to the first or second function. The
more precise way to reference functions like this is to use the
`methods` property on the contract instance. The following is an example
of how you access these methods:

<div>

    await instance.methods["setValue(string,string)"](
      "this is not string cheese",
      "this is string cheese"
    );

</div>

The `methods` property is an object whose keys are strings that
correspond to the contract function\'s names and signatures with no
spaces. The values are the functions themselves. You can inspect the
`methods` property to see the key names in this object and match the
function signature. We recommend using this method of calling overloaded
functions since it will unambiguously resolve to the correct function.

#### Processing transaction results[¶](#processing-transaction-results "Permanent link")

When you make a transaction, you\'re given a `result` object that gives
you a wealth of information about the transaction. You\'re given the
transaction hash (`result.tx`), the decoded events (also known as logs;
`result.logs`), and a transaction receipt (`result.receipt`). In the
below example, you\'ll recieve the `ValueSet()` event because you
triggered the event using the `setValue()` function:

<div>

    const result = await instance.setValue(5);
    // result.tx => transaction hash, string
    // result.logs => array of trigger events (1 item in this case)
    // result.receipt => receipt object

</div>

#### Sending Ether / Triggering the fallback function[¶](#sending-ether-triggering-the-fallback-function "Permanent link")

You can trigger the fallback function by sending a transaction to this
function:

<div>

    const result = instance.sendTransaction({...});
    // Same result object as above.

</div>

This is promisified like all available contract instance functions, and
has the same API as `web3.eth.sendTransaction` without the callback. The
`to` value will be automatically filled in for you.

If you only want to send Ether to the contract a shorthand is available:

<div>

    const result = await instance.send(web3.toWei(1, "ether"));
    // Same result object as above.

</div>

#### Estimating gas usage[¶](#estimating-gas-usage "Permanent link")

Run this function to estimate the gas usage:

<div>

    const result = instance.setValue.estimateGas(5);
    // result => estimated gas for this transaction

</div>
:::

</div>
