# React Native

<mark style="color:red;">**It is strongly discouraged to use private key or mnemonic import/generate function, if you use it, you need to secure the data yourself, Particle's SDK has no relationship with the imported/generated mnemonic or private key.**</mark>

### 1.Add the Connect Service SDK to Your React Native App <a href="#add-sdks" id="add-sdks"></a>

Run this command:

```dart
npm install @particle-network/rn-connect
```

click [here](https://github.com/Particle-Network/particle-react-native/tree/master/particle-connect) to get the demo source code&#x20;

### 2.Configure Android project

### Prerequisites <a href="#prerequisites" id="prerequisites"></a>

Make sure that your project meets the following requirements:

* Targets API Level 23 (Marshmallow) or higher
* Uses Android 6.0 or higher
* Uses [Jetpack (AndroidX)](https://developer.android.com/jetpack/androidx/migrate?authuser=0)
* Java 11

Configure project information，Refer to [here](https://github.com/Particle-Network/particle-react-native/blob/master/particle-auth/example/android/app/build.gradle)

```gradle

android {
    ...
    defaultConfig {
        ......
        manifestPlaceholders["PN_PROJECT_ID"] = "your project id"
        manifestPlaceholders["PN_PROJECT_CLIENT_KEY"] = "your project client key"
        manifestPlaceholders["PN_APP_ID"] = "your app id"
    }
   
   //How you quote wallet sdk, you must set it
    dataBinding {
        enabled = true
    }

}

```

### 3.Configure iOS project

### Prerequisites <a href="#prerequisites" id="prerequisites"></a>

* Install the following:
  * Xcode 14.1 or later.
* Make sure that your project meets the following requirements:
  * Your project must target these platform versions or later:
    * iOS 14

3.1 After export iOS project, open `your_project_name.xcworkspace` under ios folder, here its name is `ParticleConnectExample.xcworkspace.`

![](<../../../.gitbook/assets/image (3) (1).png>)

3.2 Create a **ParticleNetwork-Info.plist** into the root of your Xcode project, and make sure the file is checked under Target Membership.

![](<../../../.gitbook/assets/image (1) (4).png>)

3.3 Copy the following text into this file:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>PROJECT_UUID</key>
	<string>YOUR_PROJECT_UUID</string>
	<key>PROJECT_CLIENT_KEY</key>
	<string>YOUR_PROJECT_CLIENT_KEY</string>
	<key>PROJECT_APP_UUID</key>
	<string>YOUR_PROJECT_APP_UUID</string>
</dict>
</plist>

```

3.4 Replace `YOUR_PROJECT_UUID`, `YOUR_PROJECT_CLIENT_KEY`, and `YOUR_PROJECT_APP_UUID` with the new values created in your Dashboard

3.5. Import the `particle-auth` module in your `AppDelegate.m` file.

{% tabs %}
{% tab title="Objective-C" %}
```swift
#import <react_native_particle_connect/react_native_particle_connect-Swift.h>
```
{% endtab %}
{% endtabs %}

3.6. Add the scheme URL handle in your app's `application(_:open:options:)` method

{% tabs %}
{% tab title="Objective-C" %}
```swift
- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options {
  if ([ParticleConnectSchemeManager handleUrl:url] == YES) {
    return YES;
  } else {
    // other methods
  }
  return YES;
}
```
{% endtab %}
{% endtabs %}

3.7. Configure your app scheme URL, select your app from `TARGETS`,  under `Info` section, click + to add the `URL types`, and paste your scheme in `URL Schemes`

Your scheme URL should be "pn" + your project app uuid.

For example, if your project app id is "63bfa427-cf5f-4742-9ff1-e8f5a1b9828f", your scheme URL is "pn63bfa427-cf5f-4742-9ff1-e8f5a1b9828f".

![Config scheme url](<../../../.gitbook/assets/image (1) (2) (1).png>)

3.8 In Xcode right-click your `Info.plist` file and choose "Open As Source Code".

Copy & Paste the XML snippet into the body of your file (`<dict>`...`</dict>`).

<pre><code>&#x3C;key>LSApplicationQueriesSchemes&#x3C;/key>
&#x3C;array>
<strong>    &#x3C;string>imtokenv2&#x3C;/string>
</strong>    &#x3C;string>metamask&#x3C;/string>
    &#x3C;string>phantom&#x3C;/string>
    &#x3C;string>bitkeep&#x3C;/string>
    &#x3C;string>trust&#x3C;/string>
    &#x3C;string>rainbow&#x3C;/string>
    &#x3C;string>zerion&#x3C;/string>
    &#x3C;string>mathwallet&#x3C;/string>
    &#x3C;string>1inch&#x3C;/string>
    &#x3C;string>awallet&#x3C;/string>
    &#x3C;string>okex&#x3C;/string>
&#x3C;/array>
</code></pre>

3.9 Edit Podfile, you should follow [Podfile required](ios.md#edit-podile) to edit Podfile.

### Initialize the SDK

### Usage

```typescript
import * as particleConnect from '@particle-network/rn-connect';
```

### Initialize the SDK

**Before using the sdk you have to call init(Required)**&#x20;

```javascript
// Get your project id and client key from dashboard,  
// https://dashboard.particle.network/
ParticleInfo.projectId = ''; // your project id
ParticleInfo.clientKey = ''; // your client key 
```

{% hint style="info" %}
## Migrating to WalletConnect v2

Starting from version 0.14.0, WalletConnectV2 is supported.

```dart
// Init particle connect SDK,
// metadata is your app info, will pass to wallet when wallet connect.
// you can use the values that we provided in example.
const chainInfo = ChainInfo.EthereumGoerli;
const env = Env.Dev;

const metadata = new DappMetaData('75ac08814504606fc06126541ace9df6',
            'Particle Connect',
            'https://connect.particle.network/icons/512.png',
            'https://connect.particle.network',
            'Particle Wallet', "", "");

particleConnect.init(chainInfo, env, metadata);

// set wallet connect support chaininfos, if you dont call this method
// default value is current chaininfo.
const chainInfos = [Ethereum, Polygon, EthereumGoerli, EthereumSepolia];
particleConnect.setWalletConnectV2SupportChainInfos(chainInfos);

```
{% endhint %}

### Connect

### Web3 provider

you can use our SDK as a web3 provider

```typescript
import Web3 from 'web3';

// Start with new web3, at this time, you don't connect with this walletType, and dont know any publicAddress
var newWeb3: Web3;

// After connected a wallet, restoreWeb3 when getAccounts.
// We need to check if the walletType and publicAddress is connected. 
var web3: Web3;

newWeb3_getAccounts = async () => {
    try {
        const accounts = await newWeb3.eth.getAccounts();
        pnaccount = new PNAccount("","",accounts[0] as string,"");
        console.log('web3.eth.getAccounts', accounts);
    } catch (error) {
        console.log('web3.eth.getAccounts', error);
    }
}

restoreWeb3_getAccounts = async () => {
    try {
        console.log('pnaccount.publicAddress ', pnaccount.publicAddress);
        web3 = restoreWeb3('your project id', 'your client key', PNAccount.walletType, pnaccount.publicAddress);
       
        const accounts = await web3.eth.getAccounts();
        console.log('web3.eth.getAccounts', accounts);
    } catch (error) {
        console.log('web3.eth.getAccounts', error);
    }
}


web3_getBalance = async () => {
    try {
        const accounts = await web3.eth.getAccounts();
        const balance = await web3.eth.getBalance(accounts[0]);
        console.log('web3.eth.getBalance', balance);
    } catch (error) {
        console.log('web3.eth.getBalance', error);
    }

}

web3_getChainId = async () => {
    try {
        const chainId = await web3.eth.getChainId();
        console.log('web3.eth.getChainId', chainId);
    } catch (error) {
        console.log('web3.eth.getChainId', error);
    }

}

web3_personalSign = async () => {
    // for persion_sign
    // don't use web3.eth.personal.sign
    try {
        const accounts = await web3.eth.getAccounts();
        const result = await web3.currentProvider.request({
            method: 'personal_sign',
            params: ['hello world', accounts[0]]
        });

        console.log('web3.eth.personal.sign', result);
    } catch (error) {
        console.log('web3.eth.personal.sign', error);
    }
}

web3_signTypedData_v4 = async () => {
    try {
        const accounts = await web3.eth.getAccounts();
        const result = await web3.currentProvider.request({
            method: 'eth_signTypedData_v4',
            params: [accounts[0], { 'your typed data v4' }]
        });
        console.log('web3 eth_signTypedData_v4', result);
    } catch (error) {
        console.log('web3 eth_signTypedData_v4', error);
    }
}

web3_sendTransaction = async () => {
    try {
        const accounts = await web3.eth.getAccounts();
        const result = await web3.eth.sendTransaction(
            {
                from: accounts[0],
                to: 'a receiver address or contract address',
                value: '1000000',
                data: '0x'
            }
        )
        console.log('web3.eth.sendTransaction', result);
    } catch (error) {
        console.log('web3.eth.sendTransaction', error);
    }

}

web3_wallet_switchEthereumChain = async () => {
    try {
        const chainId = "0x" + EvmService.currentChainInfo.chain_id.toString(16);
        const result = await web3.currentProvider.request({
            method: 'wallet_switchEthereumChain',
            params: [{ chainId }]
        })
        console.log('web3 wallet_switchEthereumChain', result);
    } catch (error) {
        console.log('web3 wallet_switchEthereumChain', error);
    }

}

web3_wallet_addEthereumChain = async () => {
    try {
        const chainId = "0x" + EvmService.currentChainInfo.chain_id.toString(16);
        const result = await web3.currentProvider.request({
            method: 'wallet_addEthereumChain',
            params: [{ chainId }]
        })
        console.log('web3 wallet_addEthereumChain', result);
    } catch (error) {
        console.log('web3 wallet_addEthereumChain', error);
    }

}
```

### Connect

```javascript
// connect example
const result = await particleConnect.connect(PNAccount.walletType);
    if (result.status) {
        console.log('connect success');
        const account = result.data;
        this.pnaccount = new PNAccount(
            account.icons,
            account.name,
            account.publicAddress,
            account.url
        );
        console.log('pnaccount = ', this.pnaccount);
    } else {
        console.log('connect failure');
        const error = result.data;
        console.log(error);
    }
};

// connect with particle, support pass more parameters
// set login type, account, support types and login form mode
const connectConfig = new ParticleConnectConfig(LoginType.Phone, '', [
        SupportAuthType.Email,
        SupportAuthType.Google,
        SupportAuthType.Apple,
    ]);

    const result = await particleConnect.connect(
        WalletType.Particle,
        connectConfig
    );
    if (result.status) {
        console.log('connect success');
        const account = result.data;
        this.pnaccount = new PNAccount(
            account.icons,
            account.name,
            account.publicAddress,
            account.url
        );
        console.log('pnaccount = ', this.pnaccount);
    } else {
        console.log('connect failure');
        const error = result.data;
        console.log(error);
    }
};
```

### Disconnect

```javascript
const publicAddress = TestAccountEVM.publicAddress;
const result = await particleConnect.disconnect(walletType, publicAddress);
if (result.status) {
    console.log(result.data);
} else {
    const error = result.data;
    console.log(error);
}
```

### IsConnected

```javascript
const publicAddress = pnaccount.publicAddress;
const isConnected = await particleConnect.isConnected(walletType, publicAddress);
console.log(isConnected);
```

### Login (Sign-In With Ethereum or Solana)

```javascript
const publicAddress = pnaccount.publicAddress;
const domain = "login.xyz";
const uri = "https://login.xyz/demo#login";
const result = await particleConnect.login(walletType, publicAddress, domain, uri);
if (result.status) {
    const message = result.data.message;
    loginSourceMessage = message;
    const signature = result.data.signature;
    loginSignature = signature
    console.log("login message:", message);
    console.log("login signature:", signature);
} else {
    const error = result.data;
    console.log(error);
}
```

### Verify

```javascript
const publicAddress = pnaccount.publicAddress;
const message = loginSourceMessage;
const signature = loginSignature;
console.log("verify message:", message);
console.log("verify signature:", signature);
const result = await particleConnect.verify(walletType, publicAddress, message, signature);
if (result.status) {
    const flag = result.data;
    console.log(flag);
} else {
    const error = result.data;
    console.log(error);
}
```

### Sign message

```typescript
const message = "Hello world!"
const publicAddress = pnaccount.publicAddress;
const result = await particleConnect.signMessage(walletType, publicAddress, message);
if (result.status) {
    const signedMessage = result.data;
    console.log(signedMessage);
} else {
    const error = result.data;
    console.log(error);
}
```

### Sign transaction

```typescript
const transaction = ""
const publicAddress = pnaccount.publicAddress;
const result = await particleConnect.signTransaction(walletType, publicAddress, transaction);
if (result.status) {
    const signedTransaction = result.data;
    console.log(signedTransaction);
} else {
    const error = result.data;
    console.log(error);
}
```

### Sign all transactions

```dart
const transactions = ["", ""]
const publicAddress = pnaccount.publicAddress;
const result = await particleConnect.signAllTransactions(walletType, publicAddress, transactions);
if (result.status) {
    const signedTransactions = result.data;
    console.log(signedTransactions);
} else {
    const error = result.data;
    console.log(error);
}
```

### Sign and send transaction

```dart
const transaction = await Helper.getEthereumTransacion(pnaccount.publicAddress);
console.log(transaction);
const publicAddress = pnaccount.publicAddress;
const result = await particleConnect.signAndSendTransaction(walletType, publicAddress, transaction);
if (result.status) {
    const signature = result.data;
    console.log("signAndSendTransaction:", signature);
} else {
    const error = result.data;
    console.log(error);
}
```

### Sign typed data

```javascript
const typedData = "{        \"types\": {            \"EIP712Domain\": [                {                    \"name\": \"name\",                    \"type\": \"string\"                },                {                    \"name\": \"version\",                    \"type\": \"string\"                },                {                    \"name\": \"chainId\",                    \"type\": \"uint256\"                },                {                    \"name\": \"verifyingContract\",                    \"type\": \"address\"                }            ],            \"Person\": [                {                    \"name\": \"name\",                    \"type\": \"string\"                },                {                    \"name\": \"wallet\",                    \"type\": \"address\"                }            ],            \"Mail\": [                {                    \"name\": \"from\",                    \"type\": \"Person\"                },                {                    \"name\": \"to\",                    \"type\": \"Person\"                },                {                    \"name\": \"contents\",                    \"type\": \"string\"                }            ]        },        \"primaryType\": \"Mail\",        \"domain\": {            \"name\": \"Ether Mail\",            \"version\": \"1\",            \"chainId\": 5,            \"verifyingContract\": \"0xCcCCccccCCCCcCCCCCCcCcCccCcCCCcCcccccccC\"        },        \"message\": {            \"from\": {                \"name\": \"Cow\",                \"wallet\": \"0xCD2a3d9F938E13CD947Ec05AbC7FE734Df8DD826\"            },            \"to\": {                \"name\": \"Bob\",                \"wallet\": \"0xbBbBBBBbbBBBbbbBbbBbbbbBBbBbbbbBbBbbBBbB\"            },            \"contents\": \"Hello, Bob!\"        }}        ";
const publicAddress = pnaccount.publicAddress;
const result = await particleConnect.signTypedData(walletType, publicAddress, typedData);
if (result.status) {
    const signature = result.data;
    console.log(signature);
} else {
    const error = result.data;
    console.log(error);
}
```

### Import private key

```javascript
const privateKey = TestAccountEVM.privateKey;
const result = await particleConnect.importPrivateKey(WalletType.EvmPrivateKey, privateKey);
if (result.status) {
    const ajavccount = result.data;
    console.log(account);
} else {
    const error = result.data;
    console.log(error);
}
```

### Import mnemonic

```javascript
const mnemonic = TestAccountEVM.mnemonic;
const result = await particleConnect.importMnemonic(WalletType.EvmPrivateKey, mnemonic);
if (result.status) {
    const account = result.data;
    console.log(account);
} else {
    const error = result.data;
    console.log(error);
}
```

### Export private key

```javascript
const publicAddress = TestAccountEVM.publicAddress;
const result = await particleConnect.exportPrivateKey(WalletType.EvmPrivateKey, publicAddress);
if (result.status) {
    const privateKey = result.data;
    console.log(privateKey);
} else {
    const error = result.data;
    console.log(error);
}
```

### Get accounts

```javascript
const accounts = await particleConnect.getAccounts(walletType);
console.log(accounts);
```

### Set chain info async&#x20;

```javascript
const chainInfo = Ethereum;
const result = await particleConnect.setChainInfoAsync(chainInfo);
console.log(result);
```

### Set chain info

```javascript
const chainInfo = Ethereum;
const result = await particleConnect.setChainInfo(chainInfo);
console.log(result);
```

### Get chain info

```javascript
const chainInfo = await ParticleConnect.getChainInfo();
console.log(chainInfo);
```

### Add ethereum chain

for how to connect a wallet, please look at this [page](../faq.md) , the principle is the same.

```javascript
addEthereumChain = async () => {
    const publicAddress = TestAccountEVM.publicAddress;
    const chainId = 80001; // polygon testnet

    const result = await particleConnect.addEthereumChain(WalletType.MetaMask, publicAddress, chainId);

    if (result.status) {
        const data = result.data;
        console.log(data);
    } else {
        const error = result.data;
        console.log(error);
    }
}
```

### Switch ethereum chain&#x20;

```javascript
switchEthereumChain = async () => {
    const publicAddress = TestAccountEVM.publicAddress;
    const chainId = 137; // polygon mainnet

    const result = await particleConnect.switchEthereumChain(WalletType.MetaMask, publicAddress, chainId);

    if (result.status) {
        const data = result.data;
        console.log(data);
    } else {
        const error = result.data;
        console.log(error);
    }
}
```

### Reconnect wallet connect wallet

only support iOS, usually use when open app, if current wallet is from wallet connect, call this method to reconnect, you can call this method anywhere anytime.

It is better to start a connection before sign.

```javascript
reconnectIfNeeded = async () => {
    const publicAddress = TestAccountEVM.publicAddress;

    const result = await particleConnect.reconnectIfNeeded(
        WalletType.MetaMask,
        publicAddress
    );

    if (result.status) {
        const data = result.data;
        console.log(data);
    } else {
        const error = result.data;
        console.log(error);
    }
};
```

