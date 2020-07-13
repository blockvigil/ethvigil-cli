# CLI Tool for EthVigil APIs [![Build Status](https://travis-ci.org/blockvigil/ethvigil-cli.svg?branch=master)](https://travis-ci.org/blockvigil/ethvigil-cli)
The guide will introduce you to the EthVigil API endpoints with the help of a CLI tool that abstracts away the underlying HTTP requests. By the end of this guide, you shall be able to perform the following:

* sign up using your exclusive EthVigil invite code
* access your account information on EthVigil
* deploy a Solidity smart contract through the CLI tool

## Binary Installation

Download the [Linux](https://github.com/blockvigil/ethvigil-cli/releases/download/preview-481bed0/ev-cli-linux.zip "Linux Zip File") or [Mac OSX](https://github.com/blockvigil/ethvigil-cli/releases/download/preview-481bed0/ev-cli-osx.zip "Mac OSX Zip File") binary. You can unzip from a terminal with the following command.
`unzip /path/to/ev-cli-<platform>.zip`

Most people would keep ev-cli in their primary user folder or set an alias for easy command-line access.

`sudo cp /path/to/ev-cli /usr/local/bin/`

## Installation (via pip)

```bash
# clone the git repo
git clone https://github.com/BlockVigil/ethvigil-cli.git
cd ethvigil-cli
pip install -e .
```

## Setting up your account

There are a few handy paths you can take to setup or initialize your account on the EthVigil CLI. Choose one of them from the following as it fits your scenario.

### ✅ Generate a new EthVigil invite code and complete signup on CLI

Head to [EthVigil Beta](https://beta.ethvigil.com/?clisignup=true) to generate an invite code. Next you will need to run an init on the CLI tool.

```bash
ev-cli init
```

>If you do not have ev-cli in your path and/or calling it directly, you will need to prefix `./`

```bash
./ev-cli init
```


On the next prompt you will be asked for your invite code. The invite code should have been sent to the email address you used to register.

`Enter your invite code: <invite-code-goes-here>`

You should see something like the following

```
http://beta.ethvigil.com/api/signup
{"success":true,"data":{"status":"active","email":"swaroop@ethvigil.com"}}
Sign up succeeded...
Logging in with your credentials...
You have signed up and logged in successfully to EthVigil Beta
---YOU MIGHT WANT TO COPY THESE DETAILS TO A SEPARATE FILE---
===Private key (that signs messages to interact with EthVigil APIs===
0xprivatekeyhexstring
===ETHEREUM hexadecimal address corresponding to above private key===
0xdFaFF6081f4544fEb76d213DEB2f9DC3C8453b6
```
On EthVigil APIs, you are primarily idenitified by the above 160 bit address that we have generated locally for you.
`0xdFaFF6081f4544fEb76d213DEB2f9DC3C8453b6` in this case.

You can import the corresponding private key to a wallet solution like MetaMask.

>The keys are locally stored and EthVigil does not have access to them. You can check the `~/.ethvigil/settings.json` file once init is complete.

>Feel free to skip to the section **[`Get your EthVigil account information`](README.md#get-your-ethvigil-account-information)** if you have completed your account setup following the above instructions.

### ✅ Reuse your in-browser local wallet settings (No Metamask involved)

If you are already signed up [on the Web UI with a local wallet](https://ethvigil.com/docs/web_onboarding/#sign-up-with-invite-code), you can export a `settings.json` file that can be used to initialize the CLI.

Use the `Export Key` option from the `Settings` dropdown to download the `settings.json`

![export EthVigil settings from Web UI screenshot](https://ethvigil.com/docs/assets/cli/exportsettings.png)

Next run the following command on your terminal to import these settings

```bash
ev-cli importsettings path/to/settings.json
```

>If you do not have ev-cli in your path and/or calling it directly, you will need to prefix `./`

```bash
./ev-cli importsettings path/to/settings.json
```

And that's it! Your CLI tool is now ready for further use. The GIF below demonstrates these instructions in action.

![](https://ethvigil.com/docs/assets/cli/ev_cli_importsettings.gif)

## Get your EthVigil account information

Once initialized, you can find information related to your EthVigil account through the CLI tool.

```
ev-cli accountinfo

Contracts events fired to registered hooks: 	 {'usage': 0, 'limit': 1000}
=============

Registered integrations/hooks: 	 {'usage': 1, 'limit': 10}
=============

EthVigil API key: 	 80340b2a-633b-4a33-898c-06055ee10a34
=============

REST API prefix: 	 http://localhost:9000/api/v0.1
=============

Contracts deployed/verified:
=============
Name: SignerControlBase
Address: 0x746254cb1888a0f073fca2cf397457fb3e54396f
--------------------
Name: ERC20Mintable
Address: 0xaec35285e21045bd4f159165015cc1f9df14c13e
--------------------
```

## Deploy a Solidity smart contract

We have included a couple of smart contracts written in Solidity in the code repo to help you test out their deployment right away.
You can find them under `contracts/` as `ERC20Mintable.sol` and `SignerControlBase.sol`

The syntax to deploy a contract through the CLI tool is:

```bash
ev-cli deploy <path-to-solidity-contract> \
 --contractName=<contract-name> \
 --constructorInputs='JSON representation of the constructor arguments'
```
>Currently EthVigil API accepts Solidity files that import other Solidity files containing smart contracts and library code, **within the same directory**. For example, your imports must be of the form `import './SafeMath.sol'` denoting that `SafeMath.sol` is to be found in the same directory.

>We will soon add support for parsing relative import paths as well. Feel free to create a pull request against our [Github repo](https://github,com/blockvigil/ethvigil-cli) or chat with us on the [public discord channel](https://discord.gg/5zaS3fv) if you wish to contribute to solving this.

### ERC20 token contract example - ERC20Mintable.sol
```bash
ev-cli deploy contracts/ERC20Mintable.sol --contractName=ERC20Mintable --constructorInputs='["TestTokenName", "SYMB", 18]'

Contract ERC20Mintable deployed successfully
Contract Address: 0xaec35285e21045bd4f159165015cc1f9df14c13e
Deploying tx: 0x17a8009565731f45a1621905a7e85e84a6330b485ac3e7e450d90f126b6c3006
```
Observe that we are setting `--constructorInputs`. It is optional for contracts that have no constructor inputs programmed.

If you do not pass the `--constructorInputs` argument, you shall be prompted for the same.

```
ev-cli deploy contracts/ERC20Mintable.sol --contractName='ERC20Mintable'

Enter constructor inputs...
name(string): TestToken
symbol(string): TTK
decimals(uint8): 18
Contract ERC20Mintable deployed successfully
Contract Address: 0x9290b03870b0c4c99cc3c1e1dfcfa1ff789af6c0
Deploying tx: 0x699af417f4349f9e29d63dbc894874b5ae865fefe8e7a6bb2365339fab774211
```

### SignerControlBase.sol

This contract forms the base of [EthVigil's Proxy+Signer Control contract](https://medium.com/blockvigil/signer-control-cum-proxy-smart-contract-a-look-at-ethvigils-latest-offering-9ad6c098c095). Without going into the logic of the contract, let us take a look at the constructor as written in the contract.

```
constructor (address[] memory _primaryOwners, address[] memory _secondaryOwners, uint _required_confirmations)
    public
```

#### ⚠️Passing JSON serialized constructor inputs to the CLI tool
>This section deals with passing constructor inputs via the CLI tool. It is always easier to do the same with code.
There can be compounding confusion since
* individual constructor parameters that are arrays are expected by the EthVigil API to be encoded as strings
* the CLI tool itself accepts all the inputs as an array string.


An equivalent representation of the constructor inputs in JSON would look like
```json
[
  "[\"0x774246187E1E2205C5920898eEde0945016080Df\", \"0x902Abade63A5CB1b503Fe389aEA5906D18DAAF2b\"]",
  "[\"0x0228c246170f010C386f49e2dbc7aA999384B399\", \"0x5747Ca27b1031D8054cB9Cbc79F70CD2d9D2E204\"]",
  2
]
```

**Example to generate the JSON serialized string with Python follows**
```python
>>> import json
>>> _primary = ["0x774246187E1E2205C5920898eEde0945016080Df", "0x902Abade63A5CB1b503Fe389aEA5906D18DAAF2b"],  # primary owners
>>> _secondary = ["0x0228c246170f010C386f49e2dbc7aA999384B399", "0x5747Ca27b1031D8054cB9Cbc79F70CD2d9D2E204"] # secondary owners
>>> inputs = [
  json.dumps(_primary), json.dumps(_secondary), 2  # _required_confirmations
]
# write the entire list of arguments as a JSON serialized string into a file
>>> with open('c_inputs', 'w') as f:
        json.dump(inputs, f)
```
We dumped the JSON representation of the entire array of constructor inputs into a file, `c_inputs`.
We shall pass this next to the command line option of `--constructorInputs` in our call to deploy.


```bash
ev-cli deploy SignerControlBase.sol --contractName SignerControlBase --constructorInputs="$(< c_inputs )" --verbose=1

Got constructor inputs:
["[\"0x774246187E1E2205C5920898eEde0945016080Df\", \"0x902Abade63A5CB1b503Fe389aEA5906D18DAAF2b\"]", "[\"0x0228c246170f010C386f49e2dbc7aA999384B399\", \"0x5747Ca27b1031D8054cB9Cbc79F70CD2d9D2E204\"]", 2]
EthVigil deploy response:
{"success":true,"data":{"contract":"0x746254cb1888a0f073fca2cf397457fb3e54396f","gas":"infinite","hash":"0xcb2cb6f036e01eb22707084f4780d731ee959a50fe6b6a562643cfa40f3d5e2f"}}
Contract SignerControlBase deployed successfully
Contract Address: 0x746254cb1888a0f073fca2cf397457fb3e54396f
Deploying tx: 0xcb2cb6f036e01eb22707084f4780d731ee959a50fe6b6a562643cfa40f3d5e2f
```

## Adding integrations
You can add integrations like webhooks/email notifications/slack notifications on a contract deployed via EthVigil APIs.
* You can monitor
  * specific/all events being emitted on a contract
  * all transactions taking place on a contract
* You receive the relevant payloads like event topics or transaction input data

### Webhooks
To test webhooks, it is suggested to set up a webhook listener on a service like https://webhook.site
>EthVigil supports only HTTPS enabled webhook endpoints

Register the webhook endpoint to get an ID by which the endpoint willl be identified
```
ev-cli registerhook 0xbbd8cda5503e1df2983f738ad131a2304528e3dd https://webhook.site/9facc063-beb1-483f-b24a-408375e5d1b6

{"success":true,"data":{"id":10}}
Succeeded in registering webhook with Ethvigil API...
EthVigil Hook ID: 10
```
#### Subscribe to the `Transfer` and `Approval` events on the contract

```
ev-cli addhooktoevent 0xbbd8cda5503e1df2983f738ad131a2304528e3dd 10 Transfer,Approval

Registering | hook ID: 10 | events: ['Transfer', 'Approval'] | contract: 0xbbd8cda5503e1df2983f738ad131a2304528e3dd
{"success":true}
Succeeded in adding hook
 ```
#### Subscribe to all events, with a wildcard, *

```
ev-cli addhooktoevent 0xbbd8cda5503e1df2983f738ad131a2304528e3dd 10 '*'

Registering | hook ID: 10 | events: ['*'] | contract: 0xbbd8cda5503e1df2983f738ad131a2304528e3dd
{"success":true}
Succeeded in adding hook
```
#### Subscribe to all transactions sent to the contract
```
ev-cli enabletxmonitor 0xbbd8cda5503e1df2983f738ad131a2304528e3dd 10

{"success":true}
Succeded in adding hook to monitor all contract txs
```

## More CLI features
### Dump local settings
```
ev-cli dumpsettings

{'PRIVATEKEY': '0xprivatekeyhexstring', 'INTERNAL_API_ENDPOINT': 'https://beta.ethvigil.com/api', 'REST_API_ENDPOINT': 'http://localhost:9000/api/v0.1', 'ETHVIGIL_USER_ADDRESS': '0x40b93b89f89c674fB97db61d4b2D9CE2C2Cf6EB6', 'ETHVIGIL_API_KEY': '80340b2a-633b-4a33-898c-06055ee10a34'}
```
### Reset
If you wish to begin with a fresh slate, run the tool with the `reset` argument.
>You will lose all saved state, including the private key used to identify your account on EthVigil.
You should either backup the information or request a new invite code

```bash
ev-cli reset

Do you want to reset the current EthVigil CLI configuration and state? [y/N]:
```
### Backup settings and recover later

```bash
ev-cli dumpsettings > settings-backup.json

ev-cli reset

ev-cli importsettings setting-backup.json
```
