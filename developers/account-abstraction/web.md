# Web

## Installation[​](https://docs.walletconnect.com/1.0/#getting-started) <a href="#getting-started" id="getting-started"></a>

Install and import the biconomy package from the Particle Network SDK

```sh
yarn add @particle-network/aa
//or
npm install @particle-network/aa
```

## Initialize the SmartAccount

You need to create a Particle project to connect to your app, Visit [Particle Dashboard](../../getting-started/dashboard/) to learn more about Particle projects and apps.

And create a Biconomy Paymasters to connect AA, Visit [Biconomy Dashboard](https://dashboard.biconomy.io/) to learn more.

The provider is [EIP-1193: Ethereum Provider JavaScript API ](https://eips.ethereum.org/EIPS/eip-1193), you can use Particle Provider, MetaMask Provider or any EVM wallet provider.

```typescript
import { SmartAccount } from '@particle-network/aa';

//provider: eip1193
const smartAccount = new SmartAccount(provider, {
    projectId: 'particle project id',
    clientKey: 'particle client key',
    appId: 'particle app id',
    networkConfig: [
        { dappAPIKey: 'biconomy api key', chainId: 1 },
        { dappAPIKey: 'biconomy api key', chainId: 5 }
    ],
});
```

## Get Smart Account Address

Once you have connected your EOA signer and created an instance of Smart Account, the address is the public property and remains the same across all EVM chains.

<pre class="language-typescript"><code class="lang-typescript">// AA address
<strong>const address = await smartAccount.getAddress();
</strong>// EOA address
const address = await smartAccount.getOwner();
// load account more info.
const accountInfo = await smartAccount.getAccount();
</code></pre>

## Get Fee Quotes

Before send transactions, you can get fee quotes and display on UI Modal, users can choose which token to use to pay gas fees.

<pre class="language-typescript"><code class="lang-typescript">const tx = {
    to: '0x...',
    value: '0x...'
}
// or bacth transactions
const txs = [
    {
        to: '0x...',
        value: '0x...'
    },
    {
        to: '0x...',
        value: '0x...'
    }
]
//get fee quotes with tx or txs
const feeQuotesResult = await smartAccount.getFeeQuotes(tx);

// gasless transaction userOp, maybe null
const gaslessUserOp = feeQuotesResult.verifyingPaymasterGasless?.userOp;
<strong>const gaslessUserOpHash = feeQuotesResult.verifyingPaymasterGasless?.userOpHash;
</strong><strong>
</strong><strong>// pay with Native tokens: transaction userOp
</strong>const paidNativeUserOp = feeQuotesResult.verifyingPaymasterNative?.userOp;
const paidNativeUserOpHash = feeQuotesResult.verifyingPaymasterNative?.userOpHash;

// pay with ERC-20 tokens: fee quotes
<strong>const tokenPaymasterAddress = feeQuotesResult.tokenPaymaster.tokenPaymasterAddress;
</strong>const tokenFeeQuotes = feeQuotesResult.tokenPaymaster.feeQuotes;

</code></pre>

## Build User Operation

Builds a `UserOp` object from the provided array of `Transaction`s. It also accepts optional `feeQuote` and `tokenPaymasterAddress`. Returns a Promise that resolves to the `UserOpBundle`.

```typescript
// build user operation, feeQuote and tokenPaymasterAddress is optional.
const userOpBundle = await smartAccount.buildUserOperation({ tx, feeQuote, tokenPaymasterAddress })
const userOp = userOpBundle.userOp;
const userOpHash = userOpBundle.userOpHash;

```

### Send User Operation

Sends the pre-signed `UserOperation` to the Particle network for execution. Returns a Promise that resolves to a `TransactionHash`.

```typescript
// Some code
const txHash = await smartAccount.sendUserOperation({ userOp, userOpHash });
```

## Wallet Deployment flow

Any gasless or user paid transaction will check whether the wallet is deployed or not. If the wallet is not deployed, the SDK will batch the wallet deployment with the intended transaction/batch transactions. This behaviour is handled within the SDK itself & would require no changes in your code to enable this.

### Deploy Wallet Contract Manual

```typescript
// check the constract is deployed
const isDeploy = await smartAccount.isDeployed();
if (!isDeploy) {
    const txHash = await smartAccount.deployWalletContract();
}
```

## Wrap Provider

If your app has implemented sending transactions, or use web3.js, you can wrap the EIP-1193 provider to quickly implement AA wallet.

<pre class="language-typescript"><code class="lang-typescript">import { AAWrapProvider, SendTransactionMode, SendTransactionEvent } from '@particle-network/aa';
import Web3 from "web3";

<strong>// sendTxMode: UserPaidNative(default), Gasless, UserSelect
</strong><strong>const wrapProvider = new AAWrapProvider(smartAccount, SendTransactionMode.UserPaidNative);
</strong><strong>// replace any EIP-1193 provider to wrapProvider
</strong><strong>const web3 = new Web3(wrapProvider);
</strong>//send user paid transaction
<strong>await web3.eth.sendTransaction(tx);
</strong><strong>
</strong><strong>
</strong><strong>// send gassless transaction
</strong>const wrapProvider = new AAWrapProvider(smartAccount, SendTransactionMode.Gasless);
const web3 = new Web3(wrapProvider);
await web3.eth.sendTransaction(tx);
<strong>
</strong><strong>
</strong><strong>// use select pay gas token or gasless
</strong>const wrapProvider = new AAWrapProvider(smartAccount, SendTransactionMode.UserSelect);
const web3 = new Web3(wrapProvider);
wrapProvider.once(SendTransactionEvent.Request, (feeQuotesResult) => {
    // let the user select the pay gas ERC-20 token
    wrapProvider.resolveSendTransaction({
        feeQuote: feeQuotesResult.tokenPaymaster.feeQuote[0],
        tokenPaymasterAddress: feeQuotesResult.tokenPaymaster.tokenPaymasterAddress
    });
    
    // or pay with native token
    wrapProvider.resolveSendTransaction(feeQuotesResult.verifyingPaymasterNative);
    
    // or send gasless
    if (feeQuotesResult.verifyingPaymasterGasless) {
        wrapProvider.resolveSendTransaction(feeQuotesResult.verifyingPaymasterGasless);
    }
      
    // or reject send transaction
    wrapProvider.rejectSendTransaction({ message: 'user rejected'});
});
await web3.eth.sendTransaction(tx);
<strong>
</strong></code></pre>
