<div>

::: {md-component="content"}
# Truffle quickstart[¶](#truffle-quickstart "Permanent link")

This page will take you through the basics of creating a Truffle project
and deploying a smart contract to a blockchain.

**Note**: Before you begin, ensure that you\'ve read the [official
Ethereum documentation](https://ethereum.org/en/what-is-ethereum/).

## Install Truffle[¶](#install-truffle "Permanent link")

Before you can use Truffle, install it using the `npm` command. Refer to
the [installation instructions](../how-to/install/) to install Truffle.

## Create a project[¶](#create-a-project "Permanent link")

You need to run most Truffle commands against an existing Truffle
project. So the first step is to create a Truffle project.

You can create a bare project without smart contracts using
`truffle init`, but for those getting started, you can use [Truffle
Boxes](/boxes), which are example applications and project templates.
We\'ll use the [MetaCoin box](/boxes/metacoin), which creates a token
that can be transferred between accounts. Note that this is **not**
ERC-20 compatible.

1.  Download (\"unbox\") the MetaCoin box:

<div>

    truffle unbox metacoin [PATH/TO/DIRECTORY]

</div>

Once this operation is completed, you\'ll now have a project structure
with the following items:

-   `contracts/`: Directory for [Solidity
    contracts](/docs/truffle/getting-started/interacting-with-your-contracts)
-   `migrations/`: Directory for [scriptable deployment
    files](/docs/truffle/getting-started/running-migrations#migration-files)
-   `test/`: Directory for test files for [testing your application and
    contracts](/docs/truffle/testing/testing-your-contracts)
-   `truffle.js`: Truffle [configuration
    file](/docs/truffle/reference/configuration)

## Explore the project[¶](#explore-the-project "Permanent link")

**Note**: This page is just a quickstart, so we\'re not going to go into
much detail here. We\'ll be going over building a truffle project from
the command line. All of these commands can be executed through our VS
Code extension as well!

1.  Open the `contracts/MetaCoin.sol` file in a text editor. This is a
    smart contract (written in Solidity) that creates a MetaCoin token.
    Note that this also references another Solidity file
    `contracts/ConvertLib.sol` in the same directory.

2.  Open the `migrations/1_deploy_contracts.js` file. This file is the
    migration (deployment) script.

3.  Open the `test/TestMetaCoin.sol` file. This is a [test file written
    in Solidity](/docs/truffle/testing/writing-tests-in-solidity) which
    ensures that your contract is working as expected.

4.  Open the `test/metacoin.js` file. This is a [test file written in
    JavaScript](/docs/truffle/testing/writing-tests-in-javascript) which
    performs a similar function to the Solidity test above. The box does
    not include one, but Truffle tests can also be written in
    typescript.

5.  Open the `truffle-config.js` file. This is the Truffle
    [configuration file](/docs/truffle/reference/configuration), for
    setting network information and other project-related settings. The
    file is blank, but this is okay, as we\'ll be using a Truffle
    command that has some defaults built-in.

## Test[¶](#test "Permanent link")

To run all tests, you can simply run `truffle test`. Because
`development` is commented out in `truffle-config.js`, `truffle test`
will spin up and tear down a local test instance (`ganache`). If you
want to use more of [ganache\'s
features](https://github.com/trufflesuite/ganache#readme), you can spin
up a separate instance and specify the port number in the
`truffle-config`.

<div>

    TestMetaCoin
      ✔ testInitialBalanceUsingDeployedContract
      ✔ testInitialBalanceWithNewMetaCoin

    Contract: MetaCoin
      ✔ should put 10000 MetaCoin in the first account
      ✔ should call a function that depends on a linked library
      ✔ should send coin correctly (52ms)

</div>

You can also run each test individually by calling
`truffle test ./test/TestMetaCoin.sol` and
`truffle test ./test/metacoin.js`.

**Note**: If you\'re on Windows and encountering problems running this
command, please see the documentation on [resolving naming conflicts on
Windows](/docs/truffle/reference/configuration#resolving-naming-conflicts-on-windows).

These two tests were run against the contract, with descriptions
displayed on what the tests are supposed to do.

If you\'re running into any issues, try out our [Truffle
debugger](https://trufflesuite.com/docs/truffle/getting-started/using-the-truffle-debugger/)!

## Compile[¶](#compile "Permanent link")

If you want to only compile, you can simply run `truffle compile`.

You will see the following output:

<div>

    Compiling your contracts...
    ===========================
    > Compiling ./contracts/ConvertLib.sol
    > Compiling ./contracts/MetaCoin.sol
    > Artifacts written to /Users/emilylin/dev/metacoin-box/build/contracts
    > Compiled successfully using:
    - solc: 0.8.13+commit.abaa5c0e.Emscripten.clang

</div>

## Migrate with Truffle Develop[¶](#migrate-with-truffle-develop "Permanent link")

**Note**: To use a separate [Ganache](/ganache) instance, please skip to
the next section.

To deploy our smart contracts, we\'re going to need to connect to a
blockchain. Truffle has a built-in personal blockchain that can be used
for testing. This blockchain is local to your system and does not
interact with the main Ethereum network.

You can create this blockchain and interact with it using [Truffle
Develop](/docs/truffle/getting-started/using-truffle-develop-and-the-console#truffle-develop).

1.  Run Truffle Develop:

<div>

    truffle develop

</div>

You will see the following information:

<div>

    Truffle Develop started at http://127.0.0.1:9545/

    Accounts:
    (0) 0x627306090abab3a6e1400e9345bc60c78a8bef57
    (1) 0xf17f52151ebef6c7334fad080c5704d77216b732
    (2) 0xc5fdf4076b8f3a5357c5e395ab970b5b54098fef
    (3) 0x821aea9a577a9b44299b9c15c88cf3087f3b5544
    (4) 0x0d1d4e623d10f9fba5db95830f7d3839406c6af2
    (5) 0x2932b7a2355d6fecc4b5c0b6bd44cc31df247a2e
    (6) 0x2191ef87e392377ec08e7c08eb105ef5448eced5
    (7) 0x0f4f2ac550a1b4e2280d04c21cea7ebd822934b5
    (8) 0x6330a553fc93768f612722bb8c2ec78ac90b3bbc
    (9) 0x5aeda56215b167893e80b4fe645ba6d5bab767de

    Private Keys:
    (0) c87509a1c067bbde78beb793e6fa76530b6382a4c0241e5e4a9ec0a0f44dc0d3
    (1) ae6ae8e5ccbfb04590405997ee2d52d2b330726137b875053c36d94e974d162f
    (2) 0dbbe8e4ae425a6d2687f1a7e3ba17bc98c673636790f1b8ad91193c05875ef1
    (3) c88b703fb08cbea894b6aeff5a544fb92e78a18e19814cd85da83b71f772aa6c
    (4) 388c684f0ba1ef5017716adb5d21a053ea8e90277d0868337519f97bede61418
    (5) 659cbb0e2411a44db63778987b1e22153c086a95eb6b18bdf89de078917abc63
    (6) 82d052c865f5763aad42add438569276c00d3d88a2d062d36b2bae914d58b8c8
    (7) aa3680d5d48a8283413f7a108367c7299ca73f553735860a87b08f39395618b7
    (8) 0f62d96d6675f32685bbdb8ac13cda7c23436f63efbb9d07700d8669ff12b7c4
    (9) 8d5366123cb560bb606379f90a0bfd4769eecc0557f1b362dcae9012b548b1e5

    Mnemonic: candy maple cake sugar pudding cream honey rich smooth crumble sweet treat

    ⚠️  Important ⚠️  : This mnemonic was created for you by Truffle. It is not secure.
    Ensure you do not use it on production blockchains, or else you risk losing funds.

    truffle(development)>

</div>

This shows ten accounts (and their private keys) that can be used when
interacting with the blockchain.

1.  On the Truffle Develop prompt, Truffle commands can be run by
    omitting the `truffle` prefix. For example, to run `truffle compile`
    on the prompt, type `compile`. The command to deploy your compiled
    contracts to the blockchain is `truffle migrate`. By default,
    `truffle migrate` will also run `truffle compile`, so you can just
    do the following:

<div>

    migrate

</div>

You will see the following output:

<div>

    Starting migrations...
    ======================
    > Network name:    'develop'
    > Network id:      4447
    > Block gas limit: 6721975

    1_initial_migration.js
    ======================

       Deploying 'Migrations'
       ----------------------
       > transaction hash:    0x3fd222279dad48583a3320decd0a2d12e82e728ba9a0f19bdaaff98c72a030a2
       > Blocks: 0            Seconds: 0
       > contract address:    0xa0AdaB6E829C818d50c75F17CFCc2e15bfd55a63
       > account:             0x627306090abab3a6e1400e9345bc60c78a8bef57
       > balance:             99.99445076
       > gas used:            277462
       > gas price:           20 gwei
       > value sent:          0 ETH
       > total cost:          0.00554924 ETH

       > Saving migration to chain.
       > Saving artifacts
       -------------------------------------
       > Total cost:          0.00554924 ETH

    2_deploy_contracts.js
    =====================

       Deploying 'ConvertLib'
       ----------------------
       > transaction hash:    0x97e8168f1c05fc40dd8ffc529b9a2bf45cc7c55b07b6b9a5a22173235ee247b6
       > Blocks: 0            Seconds: 0
       > contract address:    0xfb39FeaeF3ac3fd46e2123768e559BCe6bD638d6
       > account:             0x627306090abab3a6e1400e9345bc60c78a8bef57
       > balance:             99.9914458
       > gas used:            108240
       > gas price:           20 gwei
       > value sent:          0 ETH
       > total cost:          0.0021648 ETH

       Linking
       -------
       * Contract: MetaCoin <--> Library: ConvertLib (at address: 0xfb39FeaeF3ac3fd46e2123768e559BCe6bD638d6)

       Deploying 'MetaCoin'
       --------------------
       > transaction hash:    0xee4994097c10e7314cc83adf899d67f51f22e08b920e95b6d3f75c5eb498bde4
       > Blocks: 0            Seconds: 0
       > contract address:    0x6891Ac4E2EF3dA9bc88C96fEDbC9eA4d6D88F768
       > account:             0x627306090abab3a6e1400e9345bc60c78a8bef57
       > balance:             99.98449716
       > gas used:            347432
       > gas price:           20 gwei
       > value sent:          0 ETH
       > total cost:          0.00694864 ETH

       > Saving migration to chain.
       > Saving artifacts
       -------------------------------------
       > Total cost:          0.00911344 ETH

    Summary
    =======
    > Total deployments:   3
    > Final cost:          0.01466268 ETH

</div>

This shows the transaction IDs and addresses of your deployed contracts.
It also includes a cost summary and real-time status updates.

**Note**: Your transaction hashes, contract addresses, and accounts will
be different from the above.

**Note**: To see how to interact with the contract, please skip to the
next section.

## Migrate with Truffle Console[¶](#migrate-with-truffle-console "Permanent link")

While Truffle Develop is an all-in-one personal blockchain and console,
it spins up a very basic instance of ganache. You can also use [a
desktop application](/ganache), to launch your personal blockchain,
which is an easier to understand tool for those new to Ethereum and the
blockchain, as it displays much more information up-front.
Alternatively, if you want to customize your ganache instance using all
the options available to you through the [`ganache`
CLI](https://github.com/trufflesuite/ganache#readme)

The only extra step, aside from running Ganache, is that it requires
editing the Truffle configuration file to point to the Ganache instance.

1.  Open `truffle-config.js` in a text editor. Replace the content with
    the following, ensuring your port number is correct:

<div>

    module.exports = {
      networks: {
        development: {
          host: "127.0.0.1",
          port: 7545,
          network_id: "*"
        }
      }
    };

</div>

This will allow a connection using Ganache\'s default connection
parameters.

1.  Save and close that file.

2.  On the terminal, migrate the contract to the blockchain created by
    Ganache:

<div>

    truffle migrate

</div>

You will see the following output:

<div>

    ```shell
    Compiling your contracts...
    ===========================
    > Everything is up to date, there is nothing to compile.


    Starting migrations...
    ======================
    > Network name:    'development'
    > Network id:      1661545227029
    > Block gas limit: 30000000 (0x1c9c380)


    1_deploy_contracts.js
    =====================

    Deploying 'ConvertLib'
    ----------------------
    > transaction hash:    0x259332521763056a5b949d33e3f52bff424c357b56af939ca46f96baa4386729
    > Blocks: 0            Seconds: 0
    > contract address:    0x7c31fB61DC4b817A7AFF135382FC85bcc9078f5e
    > block number:        1
    > block timestamp:     1661545283
    > account:             0x0BD3Ea7C1CDE97e91e83615D7F6eF8910b3d0FEB
    > balance:             999.999468208
    > gas used:            157568 (0x26780)
    > gas price:           3.375 gwei
    > value sent:          0 ETH
    > total cost:          0.000531792 ETH


    Linking
    -------
    * Contract: MetaCoin <--> Library: ConvertLib (at address: 0x7c31fB61DC4b817A7AFF135382FC85bcc9078f5e)

    Deploying 'MetaCoin'
    --------------------
    > transaction hash:    0x86b381fa96891e619a60a75db681d8aa95648b53572acc02690c9b94c76eadae
    > Blocks: 0            Seconds: 0
    > contract address:    0x03709DE4e1a3dA52505345Bd5129b48Ac891d63B
    > block number:        2
    > block timestamp:     1661545283
    > account:             0x0BD3Ea7C1CDE97e91e83615D7F6eF8910b3d0FEB
    > balance:             999.998107289579739204
    > gas used:            416594 (0x65b52)
    > gas price:           3.266773934 gwei
    > value sent:          0 ETH
    > total cost:          0.001360918420260796 ETH

    > Saving artifacts
    -------------------------------------
    > Total cost:     0.001892710420260796 ETH

    Summary
    =======
    > Total deployments:   2
    > Final cost:          0.001892710420260796 ETH
    ```

</div>

This shows the transaction IDs and addresses of your deployed contracts.
It also includes a cost summary and real-time status updates.

**Note**: Your transaction IDs and contract addresses may be different
from the above.

1.  To interact with the contract, you can use the Truffle console. The
    Truffle console is similar to Truffle Develop, except it connects to
    an existing blockchain (in this case, the one generated by Ganache).

<div>

    truffle console

</div>

You will see the following prompt:

<div>

    truffle(development)>

</div>

## Interact with the contract[¶](#interact-with-the-contract "Permanent link")

Interact with the contract using the console in the following ways:

**Note**: We\'re using `web3.eth.getAccounts()` in these examples, which
returns a promise which resolves to an array of all the accounts
generated by the mnemonic. So, given the addresses generated by our
mnemonic above, specifying `(await web3.eth.getAccounts())[0]` is
equivalent to the address `0x627306090abab3a6e1400e9345bc60c78a8bef57`.

As of Truffle v5, the console supports async/await functions, enabling
much simpler interactions with the contract.

-   Begin by establishing both the deployed MetaCoin contract instance
    and the accounts created by either Truffle\'s built-in blockchain or
    Ganache:

<div>

    truffle(development)> let instance = await MetaCoin.deployed()
    truffle(development)> let accounts = await web3.eth.getAccounts()

</div>

-   Check the metacoin balance of the account that deployed the
    contract:

<div>

    truffle(development)> let balance = await instance.getBalance(accounts[0])
    truffle(development)> balance.toNumber()

</div>

-   See how much ether that balance is worth (and note that the contract
    defines a metacoin to be worth 2 ether):

<div>

    truffle(development)> let ether = await instance.getBalanceInEth(accounts[0])
    truffle(development)> ether.toNumber()

</div>

-   Transfer some metacoin from one account to another:

<div>

    truffle(development)> instance.sendCoin(accounts[1], 500)

</div>

-   Check the balance of the account that *received* the metacoin:

<div>

    truffle(development)> let received = await instance.getBalance(accounts[1])
    truffle(development)> received.toNumber()

</div>

-   Check the balance of the account that *sent* the metacoin:

<div>

    truffle(development)> let newBalance = await instance.getBalance(accounts[0])
    truffle(development)> newBalance.toNumber()

</div>

## Deploy to Mainnet, Testnet, and Beyond[¶](#deploy-to-mainnet-testnet-and-beyond "Permanent link")

If you want to deploy to alternative networks, consider using [Truffle
Dashboard](../how-to/use-the-truffle-dashboard/). Just call
`truffle dashboard` and deploy, test, and run the console using
`--network dashboard`.

## Continue learning[¶](#continue-learning "Permanent link")

This quickstart showed you the basics of the Truffle project lifecycle,
but there is much more to learn. Please continue on with the rest of our
[documentation](/docs) and check out our [unleashed](/unleashed) series
for the latest tutorials and interviews with industry experts!
:::

</div>
