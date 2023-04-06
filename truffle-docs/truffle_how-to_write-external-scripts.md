<div>

::: {md-component="content"}
# Write external scripts[¶](#write-external-scripts "Permanent link")

Often you may want to run external scripts that interact with your
contracts. Truffle provides an easy way to do this, bootstrapping your
contracts based on your desired network and connecting to your Ethereum
client automatically per your [project
configuration](/docs/truffle/reference/configuration).

## Command[¶](#command "Permanent link")

To run an external script, perform the following:

<div>

``` {#__code_1}
$ truffle exec <path/to/file.js>
```

</div>

Refer to [Truffle Commands
Reference](/docs/truffle/reference/truffle-commands#exec) for more
information about this command, such as what options it accepts.

## File structure[¶](#file-structure "Permanent link")

In order for external scripts to be run correctly, Truffle expects them
to export a function that takes a single parameter as a callback:

<div>

    module.exports = function(callback) {
      // TODO: implement your actions

      // invoke callback
      callback();
    }

</div>

You can do anything you\'d like within this script, so long as the
callback is invoked when the script finishes. The callback accepts an
error as its first and only parameter. If an error is provided,
execution will halt and the process will return a non-zero exit code.

## Third-party plugin commands[¶](#third-party-plugin-commands "Permanent link")

**Note**: This feature is new and still in a barebones state. Please let
us know how we can improve it!

## Plugin installation / usage[¶](#plugin-installation-usage "Permanent link")

1.  Install the plugin from NPM.

    <div>

        npm install --save-dev truffle-plugin-hello

    </div>

2.  Add a `plugins` section to your Truffle config.

    <div>

        module.exports = {
          /* ... rest of truffle-config */

          plugins: [
            "truffle-plugin-hello"
          ]
        }

    </div>

3.  Run the command

    <div>

        $ truffle run hello
        Hello, World!

    </div>

## Create a custom command plugin[¶](#create-a-custom-command-plugin "Permanent link")

1.  Implement the command as a Node module with a function as its
    default export.

Example: `hello.js`

<div>

    /**
     * Outputs `Hello, World!` when running `truffle run hello`,
     * or `Hello, ${name}` when running `truffle run hello [name]`
     * @param {Config} config - A truffle-config object.
     * Has attributes like `truffle_directory`, `working_directory`, etc.
     */
    module.exports = async (config) => {
      // config._ has the command arguments.
      // config_[0] is the command name, e.g. "hello" here.
      // config_[1] starts remaining parameters.
      if (config.help) {
        console.log(`Usage: truffle run hello [name]`);
        return;
      }

      let name = config._.length > 1 ? config._[1] : 'World!';
      console.log(`Hello, ${name}`);
    }

</div>

1.  Define a `truffle-plugin.json` file to specify the command. Example:
    `truffle-plugin.json`

    <div>

        {
          "commands": {
            "hello": "hello.js"
          }
        }

    </div>

2.  Publish to NPM

    <div>

        npm publish

    </div>

## Import Truffle as a module[¶](#import-truffle-as-a-module "Permanent link")

<div>

    const truffle = require("truffle");

</div>

Beginning with `v5.0.30`, Truffle exports the methods listed in
[core/index.js](https://github.com/trufflesuite/truffle/blob/develop/packages/core/index.js).
This means your plugin can consume the user\'s Truffle instance as a
library and access a subset of its internal command APIs.

These are useful if you need to touch several Truffle commands in
succession. For example, imagine a plugin that evaluated how a contract
system performed at different levels of solc optimization. Its workflow
might look like:

<div>

    for a range of solc settings:
       compile contracts to a temp folder
       run user's tests using the temp artifacts
       measure and save gas usage data

    aggregate data and report

</div>

The Truffle library lets you do this without making the user add
configuration or string their own commands together.

**Warning**: Truffle does not guarantee its internal APIs will follow
semver. You should be prepared for your user to run any Truffle version
and handle mismatches gracefully. By using the library **you are
entering into an agreement** to manage API volatility and other
contingencies on your users\' behalf. Some tips:\
• Always `require` Truffle in a try/catch block\
• At runtime, verify the API components you need are actually exposed\
• Consume separately published (and semver guaranteed) Truffle modules
when possible\
• Add yourself to the Truffle repo watch list on GitHub and keep abreast
of internal changes that might affect you.\
• Do not go on vacation
:::

</div>
