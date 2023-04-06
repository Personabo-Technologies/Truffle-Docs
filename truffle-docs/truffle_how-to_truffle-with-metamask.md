<div>

::: {md-component="content"}
# Truffle and MetaMask[¶](#truffle-and-metamask "Permanent link")

Before you can interact with smart contracts in a browser, make sure
they\'re compiled, deployed, and that you\'re interacting with them via
`web3` in client-side JavaScript. We recommend using the
[\@truffle/contract](https://github.com/trufflesuite/truffle/tree/master/packages/contract)
library, as it makes interacting with contracts easier and more robust.

**Note**: For more information on these topics, including using
`@truffle/contract`, check out our [Pet Shop](/guides/pet-shop)
tutorial.

Once you\'ve done the above, you\'re ready to use MetaMask.

## What is MetaMask?[¶](#what-is-metamask "Permanent link")

[MetaMask](https://metamask.io/) is the easiest way to interact with
dapps in a browser. It is an extension for Chrome or Firefox that
connects to an Ethereum network without running a full node on the
browser\'s machine. It can connect to the main Ethereum network, any of
the testnets (Ropsten, Kovan, and Rinkeby), or a local blockchain such
as the one created by [Ganache](/ganache) or Truffle Develop.

![MetaMask](/img/docs/truffle/truffle-with-metamask/metamask.png)

For development with Truffle this means we can use our dapp the same way
users will interact with it on a live network.

## Installing MetaMask[¶](#installing-metamask "Permanent link")

-   To install MetaMask for Chrome, go to the [Chrome Web
    Store](https://chrome.google.com/webstore/detail/metamask/nkbihfbeogaeaoehlefnkodbefgpgknn)
    and click the **Add to Chrome** button.

-   To install MetaMask for FireFox, go to the [Firefox Add-ons
    page](https://addons.mozilla.org/en-US/firefox/addon/ether-metamask/)
    and click the **Add to Firefox** button.

With our front-end ready to be used and MetaMask installed, we\'re ready
to see our dapp in all its glory.

## Using MetaMask with Ganache[¶](#using-metamask-with-ganache "Permanent link")

[Ganache](/ganache) is a graphical application that runs a blockchain
that can be used for testing purposes. It runs on `127.0.0.1:7545`.

**Note**: We recommend specifying `127.0.0.1` instead of `localhost`
because the address does not require a network connection and so is more
suitable for development.

### Detecting MetaMask\'s web3 injection[¶](#detecting-metamasks-web3-injection "Permanent link")

Before diving in, we\'ll need to make sure the dapp is checking for
MetaMask\'s `web3` instance and that the extension itself is configured
properly with Ganache.

MetaMask injects its own `web3` instance, so we\'ll want to make sure
we\'re checking for that. After the window has loaded perform the
following check:

<div>

    // Is there an injected web3 instance?
    if (typeof web3 !== 'undefined') {
      App.web3Provider = web3.currentProvider;
      web3 = new Web3(web3.currentProvider);
    } else {
      // If no injected web3 instance is detected, fallback to Ganache.
      App.web3Provider = new web3.providers.HttpProvider('http://127.0.0.1:7545');
      web3 = new Web3(App.web3Provider);
    }

</div>

### Setting up MetaMask[¶](#setting-up-metamask "Permanent link")

To use Ganache with MetaMask, click the MetaMask icon in your browser
and this screen will appear:

![MetaMask initial
screen](/img/docs/truffle/truffle-with-metamask/metamask-create-password.png)

*MetaMask initial screen*

Click **Import with seed phrase**. In the box marked **Wallet Seed**,
enter the mnemonic that was displayed when launching Ganache.

**Warning**: Do not use this mnemonic on the main Ethereum network
(mainnet). Make sure that you set the network to \"Private Network\"
(use the \"Custom RPC\" setting). See below for details.

Enter a password below that and click **OK**.

![MetaMask seed
phrase](/img/docs/truffle/truffle-with-metamask/metamask-seed-phrase.png)

*MetaMask seed phrase*

Now we need to connect MetaMask to the blockchain created by Ganache.
Click the menu that shows \"Main Network\" and select **Custom RPC**.

![MetaMask network
menu](/img/docs/truffle/truffle-with-metamask/metamask-select-network.png)

*MetaMask network menu*

In the box titled \"New RPC URL\" (to the right of \"New Network\")
enter `http://127.0.0.1:7545` and click **Save**.

The network name at the top will switch to say \"Private Network\".
Click the cross in the top-right of the current window close out of the
page and return to the Accounts page.

Now that we\'ve connected MetaMask to Ganache, you\'ll be taken to the
accounts screen. Each account created by Ganache is given 100 ether. The
first account should have less than the others because that account
supplies the gas for smart contract deployment. Since you\'ve deployed
your smart contract to the network, this account paid for it.

Click the account icon in the upper-right to create new accounts, the
first 10 of which will correspond to the 10 accounts displayed when you
launched Ganache.

![MetaMask
account](/img/docs/truffle/truffle-with-metamask/metamask-account1.png)

*MetaMask account*

## Using MetaMask with Truffle Develop[¶](#using-metamask-with-truffle-develop "Permanent link")

Truffle Develop is a command-line application that runs a temporary
blockchain that is also used for testing purposes. It runs on
`127.0.0.1:9545`.

**Note**: We recommend specifying `127.0.0.1` instead of `localhost`
because the address does not require a network connection and so is more
suitable for development.

Using MetaMask with Truffle Develop is very similar to that of Ganache.
The only difference is that Truffle Develop runs by default on
`127.0.0.1:9545`, so you\'ll want to edit the above web3 code to say:

<div>

    // Is there is an injected web3 instance?
    if (typeof web3 !== 'undefined') {
      App.web3Provider = web3.currentProvider;
      web3 = new Web3(web3.currentProvider);
    } else {
      // If no injected web3 instance is detected, fallback to Truffle Develop.
      App.web3Provider = new web3.providers.HttpProvider('http://127.0.0.1:9545');
      web3 = new Web3(App.web3Provider);
    }

</div>

In MetaMask, when entering the \"New RPC URL\", enter
`http://127.0.0.1:9545`.

## Using MetaMask with Ganache CLI[¶](#using-metamask-with-ganache-cli "Permanent link")

Using MetaMask with Ganache CLI is also very similar to that of Ganache.
The only difference is that Ganache CLI runs by default on
`http://127.0.0.1:8545` so you\'ll want to edit the above web3 code to
say:

<div>

    // Is there is an injected web3 instance?
    if (typeof web3 !== 'undefined') {
      App.web3Provider = web3.currentProvider;
      web3 = new Web3(web3.currentProvider);
    } else {
      // If no injected web3 instance is detected, fallback to Ganache CLI.
      App.web3Provider = new web3.providers.HttpProvider('http://127.0.0.1:8545');
      web3 = new Web3(App.web3Provider);
    }

</div>

In MetaMask, when entering the \"New RPC URL\", enter
`http://127.0.0.1:8545`.
:::

</div>
