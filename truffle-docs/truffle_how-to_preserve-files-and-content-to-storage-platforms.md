<div>

::: {md-component="content"}
# Preserve files and content to storage platforms[¶](#preserve-files-and-content-to-storage-platforms "Permanent link")

## Preserving to IPFS, Filecoin or Textile Buckets[¶](#preserving-to-ipfs-filecoin-or-textile-buckets "Permanent link")

The [`truffle preserve`
command](/docs/truffle/reference/truffle-commands#preserve) comes
preconfigured with the ability to preserve files to IPFS, Filecoin or
Textile Buckets.

### IPFS[¶](#ipfs "Permanent link")

To preserve your files to IPFS use the `--ipfs` flag.

<div>

``` {#__code_1}
$ truffle preserve ./path --ipfs [--environment <name>]
```

</div>

#### Configuration[¶](#configuration "Permanent link")

By default, the connection to IPFS is done with a local node presumed to
be running at `http://localhost:5001`. This is the default for an `ipfs`
daemon and also for `ganache filecoin`. It is possible to point to a
different IPFS node by configuing a different URL in a
`truffle-config.js` environment.

<div>

    module.exports = {
      /* ... rest of truffle-config */

      environments: {
        /* ... other environments */

        production: {
          ipfs: {
            address: 'https://ipfs.infura.io:5001'
          }
        }
      }
    }

</div>

### Filecoin[¶](#filecoin "Permanent link")

To preserve your files to Filecoin use the `--filecoin` flag.

<div>

    $ truffle preserve ./path --filecoin [--environment <name>]

</div>

#### Configuration[¶](#configuration_1 "Permanent link") {#configuration_1}

By default, the connection to Filecoin is done with a local node
presumed to be running at `http://localhost:7777/rpc/v0`. This is the
default for a mainnet or localnet Lotus or Powergate node and also for
`ganache filecoin`. It is possible to point to a different Filecoin node
by configuing a different URL in a `truffle-config.js` environment.
Besides the connection URL, you can also configure Filecoin storage deal
options such as the duration or price.

<div>

    module.exports = {
      /* ... rest of truffle-config */

      environments: {
        /* ... other environments */

        development: {
          filecoin: {
            address: 'http://localhost:1234/rpc/v0',
            token: 'AUTH_TOKEN',
            storageDealOptions: {
              epochPrice: "2500",
              duration: 518400, // 180 days
            }
          }
        }
      }
    }

</div>

### Textile Buckets[¶](#textile-buckets "Permanent link")

To preserve your files to Textile Buckets use the `--buckets` flag.

<div>

    $ truffle preserve ./path --buckets [--environment <name>]

</div>

#### Configuration[¶](#configuration_2 "Permanent link") {#configuration_2}

Textile Buckets requires some configuration in order to work with
`truffle preserve`. To get started, you need to install [Textile\'s
`hub` tool](https://docs.textile.io/hub/), register and create
authentication keys.

<div>

    hub init
    hub keys create
     - account
     - Require Signature Authentication (recommended): N

</div>

After generating these keys, they need to be added to an environment in
your `truffle-config.js` file as well as the name of the bucket that you
want to preserve your files to - it\'s possible to use an existing
bucket for this, or if it doesn\'t exist yet it will be created in the
process.

<div>

    module.exports = {
      /* ... rest of truffle-config */

      environments: {
        /* ... other environments */

        development: {
          buckets: {
            key: "MY_BUCKETS_KEY",
            secret: "MY_BUCKETS_SECRET",
            bucketName: "truffle-preserve-bucket",
          }
        }
      }
    }

</div>

## Preserving with custom preserve recipes[¶](#preserving-with-custom-preserve-recipes "Permanent link")

While Truffle comes bundled with support for IPFS, Filecoin and Textile
Buckets, additional workflows (or recipes) can be defined and used.

### Plugin installation / configuration[¶](#plugin-installation-configuration "Permanent link")

1.  Install the plugin from NPM.

    <div>

        npm install --save-dev truffle-preserve-to-my-server

    </div>

2.  Add a `plugins` section to your Truffle config.

    <div>

        module.exports = {
          /* ... rest of truffle-config */

          plugins: [
            "truffle-preserve-to-my-server"
          ]
        }

    </div>

3.  Add any required configuration options to your Truffle config if
    it\'s required by the plugin. Refer to the plugin\'s documentation
    for this.

### Plugin usage[¶](#plugin-usage "Permanent link")

After installation and configuration, the plugin\'s tag (e.g.
`--my-server`) will show up in `truffle help preserve` and can be used
with `truffle preserve`.

<div>

    truffle preserve ./path --my-server

</div>

## Creating custom preserve recipes[¶](#creating-custom-preserve-recipes "Permanent link")

Refer to the following resources to get started creating your own custom
recipes:

-   [\@truffle/preserve Typedocs](/docs/truffle/preserves)
-   [\@truffle/preserve-to-ipfs source
    code](https://github.com/trufflesuite/truffle/tree/develop/packages/preserve-to-ipfs)
-   [\@truffle/preserve-to-filecoin source
    code](https://github.com/trufflesuite/truffle/tree/develop/packages/preserve-to-filecoin)
-   [\@truffle/preserve-to-buckets source
    code](https://github.com/trufflesuite/truffle/tree/develop/packages/preserve-to-buckets)
:::

</div>
