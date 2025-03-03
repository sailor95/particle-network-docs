# React Native

### 1.Add the Auth Service SDK to Your React Native App <a href="#add-sdks" id="add-sdks"></a>

Run this command:

```dart
npm install @particle-network/rn-auth
```

click [here](https://github.com/Particle-Network/particle-react-native/tree/master/particle-auth) to get the demo source code&#x20;

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

#### Prerequisites <a href="#prerequisites" id="prerequisites"></a>

* Install the following:
  * Xcode 14.1 or later.
* Make sure that your project meets the following requirements:
  * Your project must target these platform versions or later:
    * iOS 14

3.1 After export iOS project, open `your_project_name.xcworkspace` under ios folder, here its name is `ParticleAuthExample.xcworkspace.`

![](<../../../.gitbook/assets/image (1) (1) (2).png>)

3.2 Create a **ParticleNetwork-Info.plist** into the root of your Xcode project, and make sure the file is checked under Target Membership.

<img src="../../../.gitbook/assets/image (6) (1).png" alt="" data-size="original">

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

3.5. Import the `ParticleAuthService` module in your `AppDelegate.swift` file.

{% tabs %}
{% tab title="Objective-C" %}
```objectivec
#import <react_native_particle_auth/react_native_particle_auth-Swift.h>
```
{% endtab %}
{% endtabs %}

3.6. Add the scheme URL handle in your app's `application(_:open:options:)` method

{% tabs %}
{% tab title="Objective-C" %}
```objectivec
- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options {
  if ([ParticleAuthSchemeManager handleUrl:url] == YES) {
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

3.8 Edit Podfile, you should follow [Podfile required](ios.md#edit-podfile) to edit Podfile.



{% hint style="info" %}
Expo support

if you are developing with Expo. you need add more in ios/Podfile

```ruby
post_install do |installer|
    installer.pods_project.targets.each do |target|
    # specify our SDK name and sub moudule name,
    # this is an example for react-native-particle-auth package
      if target.name == 'ParticleNetworkBase' or 
         target.name == 'ParticleAuthService' or 
         target.name == 'CryptoSwift' or 
         target.name == 'SwiftyUserDefaults'
         target.build_configurations.each do |config|
              config.build_settings['BUILD_LIBRARY_FOR_DISTRIBUTION'] = 'YES'
      end
  end
end
```
{% endhint %}

### Usage

You can get the demo source code from [here](https://github.com/Particle-Network/particle-react-native/tree/master/particle-auth)

```typescript
import * as particleAuth from "@particle-network/rn-auth";
```

### Initialize the SDK

**Before using the SDK you have to call init(Required)**&#x20;

```typescript
// Get your project id and client from dashboard,  
// https://dashboard.particle.network/
ParticleInfo.projectId = ''; // your project id
ParticleInfo.clientKey = ''; // your client key 

const chainInfo = Ethereum;
const env = Env.Production;
particleAuth.init(chainInfo, env);
```

### Web3 provider

you can use our SDK as a web3 provider

```typescript
const web3 = createWeb3('your project id', 'your client key');

web3_getAccounts = async () => { 
    const accounts = await web3.eth.getAccounts();
    console.log('web3.eth.getAccounts', accounts);
}

web3_getBalance = async () => {
    const accounts = await web3.eth.getAccounts();
    const balance = await web3.eth.getBalance(accounts[0]);
    console.log('web3.eth.getBalance', balance);
}

web3_getChainId = async () => { 
    const chainId = await web3.eth.getChainId();
    console.log('web3.eth.getChainId', chainId);
}

web3_personalSign = async () => { 
    // for persion_sign
    // don't use web3.eth.personal.sign
    const result = await web3.currentProvider.request({
        method: 'personal_sign',
        params: ['hello world']
    });
    console.log('web3.eth.personal.sign', result);
}

web3_signTypedData_v1 = async () => {
    try {
        const accounts = await web3.eth.getAccounts();
        const result = await web3.currentProvider.request({
            method: 'eth_signTypedData_v1',
            params: [[{ 'your typed data v1'}], accounts[0]]
        });
        console.log('web3 eth_signTypedData_v1', result);
    } catch (error) {
        console.log('web3 eth_signTypedData_v1', error);
    }
}

web3_signTypedData_v3 = async () => {
    try {
        const accounts = await web3.eth.getAccounts();
        const result = await web3.currentProvider.request({
            method: 'eth_signTypedData_v3',
            params: [accounts[0], { 'your typed data v3' }]
        });
        console.log('web3 eth_signTypedData_v3', result);
    } catch (error) {
        console.log('web3 eth_signTypedData_v3', error);
    }
}

web3_signTypedData_v4 = async () => {
    try {
        const accounts = await web3.eth.getAccounts();
        const result = await web3.currentProvider.request({
            method: 'eth_signTypedData_v4',
            params: [accounts[0], { 'your typed data v4 '}]
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
        const result = await web3.currentProvider.request({
            method: 'wallet_switchEthereumChain',
            params: [{ chainId: '0x61' }]
        })
        console.log('web3 wallet_switchEthereumChain', result);
    } catch (error) {
        console.log('web3 wallet_switchEthereumChain', error);
    }

}
```

### Login

```typescript
import type { LoginType, SupportAuthType } from '@particle-network/rn-auth' 

const type = LoginType.Phone;
const supportAuthType = [SupportAuthType.All];
const result = await particleAuth.login(type, '', supportAuthType);
if (result.status) {
    const userInfo = result.data;
    console.log(userInfo);
} else {
    const error = result.data;
    console.log(error);
}

// support login and sign message
const message = "Hello Particle";
const messageHex = "0x" + Buffer.from(message).toString('hex');

const authrization = new LoginAuthorization(messageHex, false);
const result = await particleAuth.login(type, '', supportAuthType, undefined, authrization);
```

### Logout

```typescript
const result = await particleAuth.logout();
if (result.status) {
    console.log(result.data);
} else {
    const error = result.data;
    console.log(error);
}
```

### Fast logout

logout silently

```typescript
const result = await particleAuth.fastLogout();
if (result.status) {
    console.log(result.data);
} else {
    const error = result.data;
    console.log(error);
}
```

### Is Login

```javascript
const result = await particleAuth.isLogin();
console.log(result);
```

### Get address

```javascript
const address = await particleAuth.getAddress();
console.log(address)
```

### Get user info

```javascript
const result = await particleAuth.getUserInfo();
const userInfo = JSON.parse(result);
console.log(userInfo);
```

### Sign message

```dart
const message = "Hello world!"
const result = await particleAuth.signMessage(message);
if (result.status) {
    const signedMessage = result.data;
    console.log(signedMessage);
} else {
    const error = result.data;
    console.log(error);
}
```

### Sign message unique

```typescript
const message = 'Hello world!';
const result = await particleAuth.signMessageUnique(message);
if (result.status) {
    const signedMessage = result.data;
    console.log(signedMessage);
} else {
    const error = result.data;
    console.log(error);
}
```

### Sign transaction

```dart
const chainInfo = await particleAuth.getChainInfo();
if (chainInfo.chain_name.toLowerCase() != "solana") {
    console.log("signTransaction only supports solana")
    return
}
const sender = await particleAuth.getAddress();
console.log("sender: ", sender);
const transaction = await Helper.getSolanaTransaction(sender);
console.log("transaction:", transaction);
const result = await particleAuth.signTransaction(transaction);
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
const chainInfo = await particleAuth.getChainInfo();
if (chainInfo.chain_name.toLowerCase() != "solana") {
    console.log("signAllTransactions only supports solana")
    return
}
const sender = await particleAuth.getAddress();
const transaction1 = await Helper.getSolanaTransaction(sender);
const transaction2 = await Helper.getSplTokenTransaction(sender);
const transactions = [transaction1, transaction2];
const result = await particleAuth.signAllTransactions(transactions);
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
const sender = await particleAuth.getAddress();
const chainInfo = await particleAuth.getChainInfo();
let transaction = "";
if (chainInfo.chain_name.toLowerCase() == "solana") { 
    transaction = await Helper.getSolanaTransaction(sender);
} else {
    transaction = await Helper.getEthereumTransacion(sender);
}
console.log(transaction);
const result = await particleAuth.signAndSendTransaction(transaction);
if (result.status) {
    const signature = result.data;
    console.log(signature);
} else {
    const error = result.data;
    console.log(error);
}
```

### Sign typed data

```dart
const chainInfo = await particleAuth.getChainInfo();
if (chainInfo.chain_name.toLowerCase() == "solana") {
    console.log("signTypedData only supports evm")
    return
}
const typedData = "[    {    \"type\":\"string\",    \"name\":\"Message\",    \"value\":\"Hi, Alice!\"    },    {    \"type\":\"uint32\",    \"name\":\"A nunmber\",    \"value\":\"1337\"    }]";

const version = "v1";

// particleAuth support typed data version v1, v3, v4
const result = await particleAuth.signTypedData(typedData, version);
if (result.status) {
    const signature = result.data;
    console.log(signature);
} else {
    const error = result.data;
    console.log(error);
}
```

### Set chain info async

Login is required, if you want to switch to another chain, call this method after login

```dart
const chainInfo = SolanaDevnet;
const result = await particleAuth.setChainInfoAsync(chainInfo);
console.log(result);
```

### Set chain info sync

if you want to switch to another chain, call this method before login.

```dart
const chainInfo = EthereumGoerli;
const result = await particleAuth.setChainInfo(chainInfo);
console.log(result);
```

### Get chain info

```javascript
const result = await particleAuth.getChainInfo();
console.log(result);
```

### Set web auth config

```javascript
const isDisplay = true;
particleAuth.setWebAuthConfig(isDisplay, Appearance.Dark);
```

### Open web wallet

```javascript
//https://docs.particle.network/developers/wallet-service/sdks/web
let webConfig = {
    supportAddToken: false,
    supportChains: [
        {
            id: 1,
            name: 'Ethereum',
        },
        {
            id: 5,
            name: 'Ethereum',
        },
    ],
};
const webConfigJSON = JSON.stringify(webConfig);
particleAuth.openWebWallet(webConfigJSON);
```

### Open account and security page

```javascript
particleAuth.openAccountAndSecurity();

// You need listen for possible errors
private openAccountAndSecurityEvent: any;

componentDidMount = () => {
    console.log('AuthDemo componentDidMount');

    if (Platform.OS === 'ios') {
        const emitter = new NativeEventEmitter(particleAuth.ParticleAuthEvent);
        this.openAccountAndSecurityEvent = emitter.addListener('securityFailedCallBack', this.securityFailedCallBack);
    } else {
        this.openAccountAndSecurityEvent = DeviceEventEmitter.addListener(
            'securityFailedCallBack',
            this.openAccountAndSecurityEvent
        );
    }
};

componentWillUnmount() {
    this.openAccountAndSecurityEvent.remove();
};


securityFailedCallBack = (result: any) => {
    console.log(result);
}
```

### Set iOS modal present style and medium screen

```javascript
// support iOS, doesn't support Android
const style = iOSModalPresentStyle.FormSheet;
particleAuth.setModalPresentStyle(style)

// request iOS 15 or later, doesn't support Android
particleAuth.setMediumScreen(isMedium);
```

### Set language

```javascript
// support EN, ZH_HANS, ZH_HANT, JA, KO
particleAuth.setLanguage(language);
```

### Set appearance

```typescript
// support Light, Dark, System
particleAuth.setAppearance(Appearance.Dark);
```

### Set fiatCoin

```typescript
// support USD, CNY, JPY, HKD, INR, KRW
particleAuth.setFiatCoin(FiatCoin.KRW);
```

### Has master password, payment password, security account&#x20;

```javascript
// get value from local user info
const hasMasterPassword = await particleAuth.hasMasterPassword();
const hasPaymentPassword = await particleAuth.hasPaymentPassword();
const hasSecurityAccount = await particleAuth.hasSecurityAccount();

// get value from remote server
getSecurityAccount = async () => {
    const result = await particleAuth.getSecurityAccount();
    if (result.status) {
        const secuirtyAccount = result.data;
        const hasMasterPassword = secuirtyAccount.has_set_master_password;
        const hasPaymentPassword = secuirtyAccount.has_set_payment_password;
        const email = secuirtyAccount.email;
        const phone = secuirtyAccount.phont;
        const hasSecurityAccount = !email || !phone;
        console.log('hasMasterPassword', hasMasterPassword, 'hasPaymentPassword', hasPaymentPassword, 'hasSecurityAccount', hasSecurityAccount);
    } else {
        const error = result.data;
        console.log(error);
    }
}
```

## EVM Service

### Write Contract

Get a write contract transaction

```dart
const from = "your public address"；
const contractAddress = "your contract address";
const methodName = "mint"; // this is your contract method name, like balanceOf, mint.
const params = []; // this is the method params.
// abi json string, you can get it from your contract developer.
// such as
// [{\"inputs\":[{\"internalType\":\"uint256\",\"name\":\"quantity\",\"type\":\"uint256\"},{\"internalType\":\"uint256\",\"name\":\"amount\",\"type\":\"uint256\"}],\"name\":\"mint\",\"outputs\":[],\"stateMutability\":\"nonpayable\",\"type\":\"function\"}]
const abiJsonString = "";
const transaction = await EvmService.writeContract(from, contractAddress, methodName, params, abiJsonString);
```

### Read contract

Read conrtact data from blockchain

```dart
const contractAddress = "0x326C977E6efc84E512bB9C30f76E30c160eD06FB";
const methodName = "balanceOf"; // this is your contract method name, like balanceOf, mint.
const params = [address]; // this is the method params.
// abi json string, you can get it from your contract developer.
// such as
// [{\"inputs\":[{\"internalType\":\"uint256\",\"name\":\"quantity\",\"type\":\"uint256\"},{\"internalType\":\"uint256\",\"name\":\"amount\",\"type\":\"uint256\"}],\"name\":\"mint\",\"outputs\":[],\"stateMutability\":\"nonpayable\",\"type\":\"function\"}]
const abiJsonString = "";
const result = await EvmService.readContract(contractAddress, methodName, params, abiJsonString);
```

### Estimated gas

Return estimated gas

```dart
const result = await EvmService.estimateGas(from, to, value, data);
```

### Get suggested gas fees

Return gas fee json object.

```dart
const result = await EvmService.suggeseGasFee();
```

### Get tokens and NFTs

Return all tokens, NFTs and native amount at this address.

```dart
const result = await EvmService.getTokensAndNFTs(publicAddress);
```

### Get tokens

Return all tokens and native amount at this address.

```dart
const result = await EvmService.getTokens(publicAddress);
```

### Get NFTs

Return all NFTs at this address.

```dart
const result = await EvmService.getNFTs(publicAddress);
```

### Get token by token addresses

Return the balance of the token specified below this address

```dart
const result = await EvmService.getTokenByTokenAddresses(publicAddress, tokenAddresses);
```

### Get transactions by address

Return all transaction at this address

```dart
const result = await EvmService.getTransactionsByAddress(publicAddress);
```

### Get price

Return token price

```dart
const result = await EvmService.getPrice(tokenAddresses, currencies);
```

### Get smart account

Require add `particle_biconomy` and enable Biconomy.

Return smart account json object

```dart
const eoaAddress = await particleAuth.getAddress();
const result = await EvmService.getSmartAccount([eoaAddress], BiconomyVersion.v1_0_0);
```
