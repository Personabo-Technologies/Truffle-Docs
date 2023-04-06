<div>

::: {md-component="content"}
# Run Migrations[¶](#run-migrations "Permanent link")

Migrations are JavaScript files that help you deploy contracts to the
Ethereum network. These files are responsible for staging your
deployment tasks, and they\'re written under the assumption that your
deployment needs will change over time. As your project evolves, you\'ll
create new migration scripts to further this evolution on the
blockchain. A history of previously run migrations is recorded on-chain
through a special `Migrations` contract, detailed below.

## Command[¶](#command "Permanent link")

To run your migrations, run the following:

<div>

``` {#__code_1}
$ truffle migrate
```

</div>

This will run all migrations located within your project\'s `migrations`
directory. At their simplest, migrations are simply a set of managed
deployment scripts. If your migrations were previously run successfully,
`truffle migrate` will start execution from the last migration that was
run, running only newly created migrations. If no new migrations exists,
`truffle migrate` won\'t perform any action at all. You can use the
`--reset` option to run all your migrations from the beginning. Other
command options are documented
[here](../reference/truffle-commands#migrate). For local testing, make
sure to have a test blockchain such as [Ganache](/ganache) configured
and running before executing `truffle migrate`. You can also use
`truffle develop` and run your migrations.

## Migration files[¶](#migration-files "Permanent link")

A simple migration file looks like this:

Filename: `4_example_migration.js`

<div>

    var MyContract = artifacts.require("MyContract");

    module.exports = function(deployer) {
      // deployment steps
      deployer.deploy(MyContract);
    };

</div>

Note that the filename is prefixed with a number and is suffixed by a
description. The numbered prefix is required in order to record whether
the migration ran successfully. The suffix is purely for human
readability and comprehension.

### artifacts.require()[¶](#artifactsrequire "Permanent link") {#artifactsrequire}

At the beginning of the migration, we tell Truffle which contracts we\'d
like to interact with via the `artifacts.require()` method. This method
is similar to Node\'s `require`, but in our case it specifically returns
a contract abstraction that we can use within the rest of our deployment
script. The name specified should match **the name of the contract
definition** within that source file. Do not pass the name of the source
file, as files can contain more than one contract.

Consider this example where two contracts are specified within the same
source file:

Filename: `./contracts/Contracts.sol`

<div>

    contract ContractOne {
      // ...
    }

    contract ContractTwo {
      // ...
    }

</div>

To use only `ContractTwo`, your `artifacts.require()` statement would
look like this:

<div>

    var ContractTwo = artifacts.require("ContractTwo");

</div>

To use both contracts, you will need two `artifacts.require()`
statements:

<div>

    var ContractOne = artifacts.require("ContractOne");
    var ContractTwo = artifacts.require("ContractTwo");

</div>

### module.exports[¶](#moduleexports "Permanent link") {#moduleexports}

All migrations must export a function via the `module.exports` syntax.
The function exported by each migration should accept a `deployer`
object as its first parameter. This object aides in deployment by both
providing a clear syntax for deploying smart contracts as well as
performing some of deployment\'s more mundane duties, such as saving
deployed artifacts for later use. The `deployer` object is your main
interface for staging deployment tasks, and its API is described at the
bottom of this page.

Your migration function can accept other parameters as well. See the
examples below.

Filename: `migrations/1_deploy_contracts.js`

<div>

    var SolidityContract = artifacts.require("SolidityContract");

    module.exports = function(deployer) {
      // Deploy the SolidityContract contract as our only task
      deployer.deploy(SolidityContract);
    };

</div>

From here, you can create new migrations with increasing numbered
prefixes to deploy other contracts and perform further deployment steps.

## Deployer[¶](#deployer "Permanent link")

Your migration files will use the deployer to stage deployment tasks. As
such, you can write deployment tasks synchronously and they\'ll be
executed in the correct order:

<div>

    // Stage deploying A before B
    deployer.deploy(A);
    deployer.deploy(B);

</div>

Alternatively, each function on the deployer can be used as a Promise,
to queue up deployment tasks that depend on the execution of the
previous task:

<div>

    // Deploy A, then deploy B, passing in A's newly deployed address
    deployer.deploy(A).then(function() {
      return deployer.deploy(B, A.address);
    });

</div>

It is possible to write your deployment as a single promise chain if you
find that syntax to be more clear. The deployer API is discussed at the
bottom of this page.

## Network considerations[¶](#network-considerations "Permanent link")

It is possible to run deployment steps conditionally based on the
network being deployed to. This is an advanced feature, so see the
[Networks](/docs/truffle/advanced/networks-and-app-deployment) section
first before continuing.

To conditionally stage deployment steps, write your migrations so that
they accept a second parameter, called `network`. Example:

<div>

    module.exports = function(deployer, network) {
      if (network == "live") {
        // Do something specific to the network named "live".
      } else {
        // Perform a different step otherwise.
      }
    }

</div>

## Available accounts[¶](#available-accounts "Permanent link")

Migrations are also passed the list of accounts provided to you by your
Ethereum client and web3 provider, for you to use during your
deployments. This is the exact same list of accounts returned from
`web3.eth.getAccounts()`.

<div>

    module.exports = function(deployer, network, accounts) {
      // Use the accounts within your migrations.
    }

</div>

## Deployer API[¶](#deployer-api "Permanent link")

The deployer contains many functions available to simplify your
migrations.

### deployer.deploy(contract, args\..., options)[¶](#deployerdeploycontract-args-options "Permanent link") {#deployerdeploycontract-args-options}

Deploy a specific contract, specified by the contract object, with
optional constructor arguments. This is useful for singleton contracts,
such that only one instance of this contract exists for your dapp. This
will set the address of the contract after deployment (i.e.,
`Contract.address` will equal the newly deployed address), and it will
override any previous address stored.

You can optionally pass an array of contracts, or an array of arrays, to
speed up deployment of multiple contracts. Additionally, the last
argument is an optional object that can include the key named
`overwrite` as well as other transaction parameters such as `gas` and
`from`. If `overwrite` is set to `false`, the deployer won\'t deploy
this contract if one has already been deployed. This is useful for
certain circumstances where a contract address is provided by an
external dependency.

Note that you will need to deploy and link any libraries your contracts
depend on first before calling `deploy`. See the `link` function below
for more details.

For more information, please see the
[\@truffle/contract](https://github.com/trufflesuite/truffle/tree/master/packages/contract)
documentation.

Examples:

<div>

    // Deploy a single contract without constructor arguments
    deployer.deploy(A);

    // Deploy a single contract with constructor arguments
    deployer.deploy(A, arg1, arg2, ...);

    // Don't deploy this contract if it has already been deployed
    deployer.deploy(A, {overwrite: false});

    // Set a maximum amount of gas and `from` address for the deployment
    deployer.deploy(A, {gas: 4612388, from: "0x...."});

    // Deploying multiple contracts as an array is now deprecated.
    // This used to be quicker than writing three `deployer.deploy()` statements as the deployer
    // can perform the deployment as a single batched request.
    // deployer.deploy([
    //   [A, arg1, arg2, ...],
    //   B,
    //   [C, arg1]
    // ]);

    // External dependency example:
    //
    // For this example, our dependency provides an address when we're deploying to the
    // live network, but not for any other networks like testing and development.
    // When we're deploying to the live network we want it to use that address, but in
    // testing and development we need to deploy a version of our own. Instead of writing
    // a bunch of conditionals, we can simply use the `overwrite` key.
    deployer.deploy(SomeDependency, {overwrite: false});

</div>

### deployer.link(library, destinations)[¶](#deployerlinklibrary-destinations "Permanent link") {#deployerlinklibrary-destinations}

Link an already-deployed library to a contract or multiple contracts.
Here `library` can be either the library contract abstraction, to link
to the deployed copy, or a specific library instance if you want to link
to a copy at a different address. The `destinations` argument can be a
single contract or an array of multiple contracts. If any contract
within the destination doesn\'t rely on the library being linked, the
contract will be ignored.

Example:

<div>

    // Deploy library LibA, then link LibA to contract B, then deploy B.
    deployer.deploy(LibA);
    deployer.link(LibA, B);
    deployer.deploy(B);

    // Link LibA to many contracts
    deployer.link(LibA, [B, C, D]);

    // Link to a copy of LibA at a custom address
    const instanceOfLibA = await LibA.at(address);
    await deployer.link(instanceOfLibA, B);

</div>

### deployer.then(function() {\...})[¶](#deployerthenfunction "Permanent link") {#deployerthenfunction}

Just like a promise, run an arbitrary deployment step. Use this to call
specific contract functions during your migration to add, edit and
reorganize contract data.

Example:

<div>

    var a, b;
    deployer.then(function() {
      // Create a new version of A
      return A.new();
    }).then(function(instance) {
      a = instance;
      // Get the deployed instance of B
      return B.deployed();
    }).then(function(instance) {
      b = instance;
      // Set the new instance of A's address on B via B's setA() function.
      return b.setA(a.address);
    });

</div>

### Migrations with async/await[¶](#migrations-with-asyncawait "Permanent link")

You can also migrate your contracts using `async/await`:

Example:

<div>

    module.exports = async function(deployer) {
      // deploy a contract
      await deployer.deploy(MyContract);
      //access information about your deployed contract instance
      const instance = await MyContract.deployed();
    }

</div>
:::

</div>
