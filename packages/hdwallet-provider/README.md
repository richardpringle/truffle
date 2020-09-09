# @truffle/hdwallet-provider
HD Wallet-enabled Web3 provider. Use it to sign transactions for addresses derived from a 12 or 24 word mnemonic.

## Install

```
$ npm install @truffle/hdwallet-provider
```

## Requirements
```
Node >= 7.6
Web3 ^1.2.0
```

## General Usage

You can use this provider wherever a Web3 provider is needed, not just in Truffle. For Truffle-specific usage, see next section.


By default, the `HDWalletProvider` will use the address of the first address that's generated from the mnemonic. If you pass in a specific index, it'll use that address instead.

### Instantiation

There are many options you can specify when instantiating. You do this by
providing a single object with named keys as the first argument.
You can specify the following options in your object:

Parameters:

| Parameter | Type | Default | Required | Description |
| ------ | ---- | ------- | ----------- | ----------- |
| `mnemonic` | `object\|string` | null | [ ] | Object containing `phrase` and `password` (optional) properties. `phrase` is a 12 word mnemonic string which addresses are created from. Alternately you can just specify a string mnemonic from which to generate addresses. |
| `privateKeys` | `string[]` | null | [ ] | Array containing 1 or more private keys. |
| `providerOrUrl` | `string\|object` | `null` | [x] | URI or Ethereum client to send all other non-transaction-related Web3 requests |
| `addressIndex` | `number` | `0` | [ ] | If specified, will tell the provider to manage the address at the index specified |
| `numberOfAddresses` | `number` | `1` | [ ] | If specified, will create `numberOfAddresses` addresses when instantiated |
| `shareNonce` | `boolean` | `true` | [ ] | If `false`, a new WalletProvider will track its own nonce-state |
| `derivationPath` | `string` | `"m/44'/60'/0'/0/"` | [ ] | If specified, will tell the wallet engine what derivation path should use to derive addresses. |

Some examples can be found below:

```javascript
const HDWalletProvider = require("@truffle/hdwallet-provider");
const Web3 = require("web3");
const mnemonicPhrase = {
  phrase: "mountains supernatural bird..."; // 12 word mnemonic
};
const password = "my secret password";
let provider = new HDWalletProvider({
  mnemonic: {
    phrase: mnemonicPhrase,
    password: password
  },
  providerOrUrl: "http://localhost:8545"
});

// Or, alternatively pass in a zero-based address index.
provider = new HDWalletProvider({
  mnemonic: mnemonicPhrase,
  provider: "http://localhost:8545",
  addressIndex: 5
});

// Or, use your own hierarchical derivation path
provider = new HDWalletProvider({
  mnemonic: mnemonicPhrase,
  provider: "http://localhost:8545",
  numberOfAddresses: 1,
  shareNonce: true,
  derivationPath: "m/44'/137'/0'/0/"
});

// HDWalletProvider is compatible with Web3. Use it at Web3 constructor, just like any other Web3 Provider
const web3 = new Web3(provider);

// Or, if web3 is alreay initialized, you can call the 'setProvider' on web3, web3.eth, web3.shh and/or web3.bzz
web3.setProvider(provider)

// ...
// Write your code here.
// ...

// At termination, `provider.engine.stop()' should be called to finish the process elegantly.
provider.engine.stop();
```

**Note: If both mnemonic and private keys are provided, the mnemonic is used.**

### Deprecated positional argument syntax for instantiation

**Note:** The following syntax is legacy syntax and deprecated. We provide the
following for clarity only. We recommend you specify
your options in an object with named keys as described above.

The legacy syntax allows you to specify the following options in the order below.
Pass `undefined` if you want to omit a parameter.

Parameters:

| Parameter | Type | Default | Required | Description |
| ------ | ---- | ------- | ----------- | ----------- |
| `mnemonic`/`privateKeys` | `string`/`string[]` | null | [x] | 12 word mnemonic which addresses are created from or array of private keys. |
| `providerOrUrl` | `string\|object` | `null` | [x] | URI or Ethereum client to send all other non-transaction-related Web3 requests |
| `addressIndex` | `number` | `0` | [ ] | If specified, will tell the provider to manage the address at the index specified |
| `numberOfAddresses` | `number` | `1` | [ ] | If specified, will create `numberOfAddresses` addresses when instantiated |
| `shareNonce` | `boolean` | `true` | [ ] | If `false`, a new WalletProvider will track its own nonce-state |
| `derivationPath` | `string` | `"m/44'/60'/0'/0/"` | [ ] | If specified, will tell the wallet engine what derivation path should use to derive addresses. |


Instead of a mnemonic, you can alternatively provide a private key or array of
private keys as the first parameter. When providing an array, `addressIndex`
and `numberOfAddresses` are fully supported.

**NOTE: NEVER hard code production/mainnet private keys in your code or commit 
them to git. They should always be loaded from environment variables or a
secure secret management system.**

## Truffle Usage

You can easily use this within a Truffle configuration. For instance:

truffle-config.js
```javascript
const HDWalletProvider = require("@truffle/hdwallet-provider");

const mnemonicPhrase = "mountains supernatural bird ...";

module.exports = {
  networks: {
    development: {
      host: "localhost",
      port: 8545,
      network_id: "*" // Match any network id
    },
    ropsten: {
      // must be a thunk, otherwise truffle commands may hang in CI
      provider: () =>
        new HDWalletProvider({
          mnemonic: {
            phrase: mnemonicPhrase
          },
          providerOrUrl: "https://ropsten.infura.io/v3/YOUR-PROJECT-ID",
          numberOfAddresses: 1,
          shareNonce: true,
          derivationPath: "m/44'/1'/0'/0/"
        }),
      network_id: '3',
    }
  }
};
```
