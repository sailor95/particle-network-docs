# iOS

## Github Demo

[https://github.com/Particle-Network/particle-connect-ios](https://github.com/Particle-Network/particle-connect-ios)

## Summary

Modular Swift wallet adapters and components for EVM & Solana chains. Manage wallet and custom RPC request.

![Particle Connect](https://static.particle.network/docs-images/particle-connect.jpeg)

<mark style="color:red;">**It is strongly discouraged to use private key or mnemonic import/generate function, if you use it, you need to secure the data yourself, Particle's SDK has no relationship with the imported/generated mnemonic or private key.**</mark>

## Quick Start

### Add Connect Service to Your iOS Project

#### Prerequisites <a href="#prerequisites" id="prerequisites"></a>

* Install the following:
  * Xcode 14.1 or later
  * CocoaPods 1.11.0 or higher
* Make sure that your project meets the following requirements:
  * Your project must target these platform versions or later:
    * iOS 14

#### Create a Particle Project and App

Before you can add our Connect Service to your iOS app, you need to create a Particle project to connect to your iOS app. Visit [Particle Dashboard](../../../getting-started/dashboard/) to learn more about Particle projects and apps.

[👉 Sign up/log in and create your project now](https://dashboard.particle.network/#/login)

#### Add the Connect Service SDK to Your App <a href="#add-sdks" id="add-sdks"></a>

Connect Service supports installation with [CocoaPods](https://guides.cocoapods.org/using/getting-started.html#getting-started).

Connect Service's CocoaPods distribution requires Xcode 14.1 and CocoaPods 1.11.0 or higher. Here's how to install the Auth Service using CocoaPods:

1. Create a Podfile if you don't already have one. From the root of your project directory, run the following command:

```ruby
pod init
```

2\. To your Podfile, add the Auth Service pods that you want to use in your app:

```ruby
pod 'ConnectCommon'
pod 'ParticleConnect'
pod 'ConnectWalletConnectAdapter'
pod 'ConnectEVMAdapter'
pod 'ConnectSolanaAdapter'
pod 'ConnectPhantomAdapter'
pod 'ParticleAuthAdapter'
```

3\. Install the pods, then open your `.xcworkspace` file to see the project in Xcode:

```ruby
pod install --repo-update
```

```ruby
open your-project.xcworkspace
```

{% hint style="info" %}
If you would like to receive release updates, subscribe to our [GitHub repository](https://github.com/Particle-Network).
{% endhint %}

### Edit Podile

{% hint style="info" %}
#### It is required for every iOS project that integrates the Auth Service SDK.

```ruby
// paste there code into pod file
post_install do |installer|
installer.pods_project.targets.each do |target|
  target.build_configurations.each do |config|
  config.build_settings['BUILD_LIBRARY_FOR_DISTRIBUTION'] = 'YES'
    end
  end
 end
```
{% endhint %}

### Initialize Connect Service in your app <a href="#initialize-firebase" id="initialize-firebase"></a>

The final step is to add an initialization code to your application. You may have already done this as part of adding the Connect Service to your app. If you are using a [quickstart sample project](https://github.com/Particle-Network/particle-ios), this has been done for you.

1. Create a **ParticleNetwork-Info.plist** into the root of your Xcode project
2. Copy the following text into this file:

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

3\. Replace `YOUR_PROJECT_UUID`, `YOUR_PROJECT_CLIENT_KEY`, and `YOUR_PROJECT_APP_UUID` with the new values created in your Dashboard

4\. Import the `ParticleNetwork` module in your `UIApplicationDelegate`

{% tabs %}
{% tab title="Swift" %}
```swift
import ConnectCommon
import ConnectEVMAdapter
import ConnectPhantomAdapter
import ConnectSolanaAdapter
import ConnectWalletConnectAdapter
import ParticleNetworkBase
import ParticleConnect
import ParticleAuthAdapter
// if you are using ParticleAuthCore
// import AuthCoreAdapter
```
{% endtab %}
{% endtabs %}

5\. Initialize the ParticleNetwork service, which is typically in your app's `application:didFinishLaunchingWithOptions:` method:

{% tabs %}
{% tab title="Swift" %}
```swift
// initialize method
// you can modify the adapters, remove the one you dont need.
var adapters: [
       ParticleAuthAdapter(),
       WalletConnectAdapter(),
       MetaMaskConnectAdapter(),
       PhantomConnectAdapter(),
       EVMConnectAdapter(),
       SolanaConnectAdapter(),
       ImtokenConnectAdapter(),
       BitkeepConnectAdapter(),
       RainbowConnectAdapter(),
       TrustConnectAdapter(),
       // if you are using ParticleAuthCore
       // AuthCoreAdaper()
       ]
        
let moreAdapterClasses: [WalletConnectAdapter.Type] =
     [ZerionConnectAdapter.self,
      MathConnectAdapter.self,
      OmniConnectAdapter.self,
      Inch1ConnectAdapter.self,
      ZengoConnectAdapter.self,
      AlphaConnectAdapter.self,
      OKXConnectAdapter.self]

adapters.append(contentsOf: moreAdapterClasses.map {
     $0.init()
})
 
ParticleConnect.initialize(env: .debug, 
 chainInfo: .ethereum(.mainnet), 
 dAppData: DAppMetaData(name: "Particle Connect", 
 icon: URL(string: "https://connect.particle.network/icons/512.png")!, 
 url: URL(string: "https://connect.particle.network")!)) {
      adapters
}

```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
## Migrating to WalletConnect v2

Starting from version 0.2.0, WalletConnectV2 is supported.

```swift
// WalletConnect 2.0 required, set wallet connect v2 project id
ParticleConnect.setWalletConnectV2ProjectId("your wallet connect v2.0 project id")
// Set the required chains for WalletConnect v2. If not set, the current chain will be used.
ParticleConnect.setWalletConnectV2SupportChainInfos([.ethereum(.mainnet), .ethereum(.goerli)])
```
{% endhint %}

6\. Add the scheme URL handle in your app's `application(_:open:options:)` method

{% tabs %}
{% tab title="Swift" %}
```swift
return ParticleConnect.handleUrl(url)
```
{% endtab %}
{% endtabs %}

7\. Configure your app scheme URL, select your app target in the info section, click to add the URL type, and pass your scheme in URL Schemes

Your scheme URL should be "pn" + your project app uuid.

For example, if your project app id is "63bfa427-cf5f-4742-9ff1-e8f5a1b9828f", your scheme URL is "pn63bfa427-cf5f-4742-9ff1-e8f5a1b9828f".

![Config scheme url](<../../../.gitbook/assets/image (1) (2) (1).png>)

8\. Add LSApplicationQueriesSchemes to info.plist.

each of them is optional, you can add which you want.

```
<key>LSApplicationQueriesSchemes</key>
<array>
	<string>metamask</string>
	<string>phantom</string>
	<string>bitkeep</string>
	<string>imtokenv2</string>
	<string>rainbow</string>
	<string>trust</string>
	
	<string>zerion</string>
        <string>mathwallet</string>
        <string>1inch</string>
        <string>awallet</string>
        <string>okex</string>
</array>
```

### Switch chain.

```swift
ParticleConnect.setChain(chainInfo: .ethereum(.mainnet))
```

### Get all wallet adapters.

```swift
let adapter = ParticleConnect.getAllAdapters()
                .filter { $0.walletType == .particle }.first

let adapters = ParticleConnect.getAdapters(chainType: .evm)

let adapter = ParticleConnect.getAdapterByAddress(publicAddress: address)
```

### Get adapter accounts.

```swift
let accounts = adapter.getAccounts()
```

### Get smart accounts

works on you enable [Account Abstraction](../../account-abstraction/ios.md)

```swift
adapter.getSmartAccounts()
```

### Connect wallet.

For `EVMConnectAdapter` or `SolanaConnectAdapter` will generate new wallet

```swift
connectAdapter.connect().subscribe { [weak self] result in
    guard let self = self else { return }
    switch result {
        case .failure(let error):
        print(error)
        case .success(let account):
        print(account)
    }.disposed(by: bag)

```

### Disconnect wallet.

```swift
connectAdapter.disconnect(address).subscribe { [weak self] result in
    guard let self = self else { return }
    switch result {
        case .failure(let error):
        print(error)
        case .success(let success):
        print(success)
    }.disposed(by: bag)

```

### Is connected

call this method to check if the public address is connected.

After user connects with a third party wallet, user can remove the wallet connect's session in the third party wallet, when receive disconnect request, local session cache should be removed.&#x20;

It is better to call `reconnectIfNeeded` when start app.

before sign or open GUI page, check the method `isConnected,` if result is disconnected, you need to connect again.

```swift
let publicAddress = getSender()
let result = adapter.isConnected(publicAddress: publicAddress)
if result {
    print("publicAddress \(publicAddress) is connected")
} else {
    print("publicAddress \(publicAddress) is disconnected")
}
```

### Reconnect if needed

```swift
// Wallet Connect Adapter need reconnect after app launch
// other sub adapter as MetaMaskConnectAdapter, RainConnectAdaper ... also need reconnect after app launch 
// call reconnectIfNeeded after app launch, or before call other sign methods.
(adapter as? WalletConnectAdapter)?.reconnectIfNeeded(publicAddress: getSender())
```

### Check whether the account is connected.

```swift
let result = connectAdapter.isConnected(address)
```

### Import wallet.

(Only `EVMConnectAdapter` and `SolanaConnectAdapter` support this method)

```swift
// import wallet with private key
connectAdapter.importWalletFromPrivateKey(privateKey).subscribe { [weak self] result in
    guard let self = self else { return }
    switch result {
        case .failure(let error):
        print(error)
        case .success(let account):
        print(account)
    }
}.disposed(by: bag)

// import wallet with mnemonic(Split with space).
connectAdapter.importWalletFromMnemonic(mnemonic).subscribe { [weak self] result in
    guard let self = self else { return }
    switch result {
        case .failure(let error):
        print(error)
        case .success(let account):
        print(account)
    }
}.disposed(by: bag)
```

### Export wallet.

(Only `EVMConnectAdapter` and `SolanaConnectAdapter` support this method)

```swift
connectAdapter.exportWalletPrivateKey(publicAddress: address).subscribe { [weak self] result in
    guard let self = self else { return }
    switch result {
        case .failure(let error):
        print(error)
        case .success(let privateKey):
        print(privateKey)
    }
}.disposed(by: bag)
```

### Sign and send transaction.

```swift
// todo: check connected before sign
connectAdapter.signAndSendTransaction(publicAddress: address, transaction: transaction).subscribe { [weak self] result in
    guard let self = self else { return }
    switch result {
        case .failure(let error):
        print(error)
        case .success(let signature):
        print(signature)
    }
}.disposed(by: bag)
```

### Sign transaction.

(Only Solana chain support this method)

```swift
connectAdapter.signTransaction(publicAddress: address, transaction: transaction).subscribe { [weak self] result in
    guard let self = self else { return }
    switch result {
        case .failure(let error):
        print(error)
        case .success(let signed):
        print(signed)
    }
}.disposed(by: bag)
```

### Sign all transactions.

(Only Solana chain support this method)

```swift
connectAdapter.signAllTransactions(publicAddress: address, transactions: transactions).subscribe { [weak self] result in
    guard let self = self else { return }
    switch result {
        case .failure(let error):
        print(error)
        case .success(let signed):
        print(signed)
    }
}.disposed(by: bag)
```

### Sign message.

(EVM call `personal_sign`)

```swift
connectAdapter.signMessage(publicAddress: address, message: message).subscribe { [weak self] result in
    guard let self = self else { return }
    switch result {
        case .failure(let error):
        print(error)
        case .success(let signed):
        print(signed)
    }
}.disposed(by: bag)
```

### Sign typed data.

(Only EVM chains support this method)

```swift
connectAdapter.signTypedData(publicAddress: address, data: data).subscribe { [weak self] result in
    guard let self = self else { return }
    switch result {
        case .failure(let error):
        print(error)
        case .success(let signed):
        print(signed)
    }
}.disposed(by: bag)
```

### Login (Sign-In With Ethereum or Solana)

```swift
// We have full example in github demo.
let message = try! getSiweMessage()
self.siweMessage = message
print("message = \(message.description)")
adapter.login(config: message, publicAddress: getSender()).subscribe { [weak self] result in
    guard let self = self else { return }
    switch result {
    case .failure(let error):
        print(error)
        if let connectError = error as? ConnectError {
            self.resultLabel.text = "code = \(String(describing: connectError.code)), message = \(String(describing: connectError.message))"
        } else {
            self.resultLabel.text = error.localizedDescription
        }
    case .success(let (sourceMessage, signedMessage)):
        print("sourceMessage = \(sourceMessage), \n\nsignedMessage = \(signedMessage)")
        self.resultLabel.text = signedMessage
    }
}.disposed(by: bag)
```

### Verify locally

<pre class="language-swift"><code class="lang-swift">// We have full example in github demo.
<strong>guard let message = siweMessage else {
</strong>    return
}
let against = resultLabel.text ?? ""
adapter.verify(message: message, against: against).subscribe { [weak self] result in
    guard let self = self else { return }
    switch result {
    case .failure(let error):
        print(error)
        if let connectError = error as? ConnectError {
            self.resultLabel.text = "code = \(String(describing: connectError.code)), message = \(String(describing: connectError.message))"
        } else {
            self.resultLabel.text = error.localizedDescription
        }
    case .success(let flag):
        print(flag)
        self.resultLabel.text = flag ? "True" : "False"
    }
}.disposed(by: bag)
</code></pre>

### Request&#x20;

request eth or solana rpc methods, from tests, wallet connect MetaMask and Rainbow works, other wallets not works.

```swift
// send rpc request eth_getBalance
let publicAddress = getSender()
let method = "eth_getBalance"
self.adapter.request(publicAddress: publicAddress, method: method, parameters: [publicAddress, "latest"]).subscribe { [weak self] result in
    guard let self = self else { return }
    switch result {
    case .failure(let error):
        print(error)
    case .success(let json):
        print(json)
    }
}.disposed(by: bag)


// get data from ParticleWalletAPI, then send rpc request eth_call
let publicAddress = getSender()
let method = "eth_call"
let contractAddress = "0xfe4F5145f6e09952a5ba9e956ED0C25e3Fa4c7F1"
ParticleWalletAPI.getEvmService().abiEncodeFunctionCall(contractAddress: contractAddress, methodName: "balanceOf", params: [publicAddress]).flatMap { json -> Single<SwiftyJSON.JSON?> in
    let data = json.stringValue
    let call = CallParams(from: publicAddress, to: contractAddress, value: nil, data: data, gas: nil, gasPrice: nil)
    return self.adapter.request(publicAddress: publicAddress, method: method, parameters: [call, "latest"])
}.subscribe { [weak self] result in
    guard let self = self else { return }
    switch result {
    case .failure(let error):
        print(error)
    case .success(let json):
        print(json)
    }
}.disposed(by: bag)
```

### Add Ethereum Chain&#x20;

From tests, wallet connect MetaMask works well, other wallets not work.

{% hint style="warning" %}
### Wallet Connect V2 doesn't support add chain or switch chain, the feature is not available anymore
{% endhint %}

```swift
// add ethereum chain
let publicAddress = getSender()
let chainInfo = ParticleNetwork.ChainInfo.bsc(.mainnet)
let chainId: Int = chainInfo.chainId

adapter.addEthereumChain(publicAddress: publicAddress, chainId: chainId, chainName: nil, nativeCurrency: nil, rpcUrl: nil, blockExplorerUrl: nil).subscribe { [weak self] result in
    guard let self = self else { return }
    
    switch result {
    case .failure(let error):
        print(error)
    case .success(let flag):
        print(flag)
    }
    
}.disposed(by: bag)
```

### Switch Ethereum Chain

{% hint style="warning" %}
### Wallet Connect V2 doesn't support add chain or switch chain, the feature is not available anymore
{% endhint %}

```swift
// switch ethereum chain
let publicAddress = getSender()
let chainInfo = ParticleNetwork.ChainInfo.polygon(.mainnet)
let chainId: Int = chainInfo.chainId

adapter.switchEthereumChain(publicAddress: publicAddress, chainId: chainId).subscribe { [weak self] result in
    guard let self = self else { return }
    
    switch result {
    case .failure(let error):
        print(error)
    case .success(let flag):
        print(flag)
    }
    
}.disposed(by: bag)
```

### Custom wallet connect adapter

```swift
/// How to define a custom wallet connect adapter
/// for examle coin98 wallet
/// 1. subclass from WalletConnectAdapter
/// 2. override walletType, provide a AdapterInfo object
/// don't use teh same redirectUrlHost with other connect adapters
/// if the app has a universal link, like metamask's is "https://metamask.app.link/"
/// set it to deeplink
/// if the app doesn't have a universal link, set scheme as the deeplink
/// if the app has neither universal link nor scheme,
/// it is not ready for wallet connect.
public class Coin98WalletConnectAdapter: WalletConnectAdapter {
    public override var walletType: WalletType {
        return WalletType.custom(info: AdapterInfo.init(name: "Coin98",
                     url: "https://coin98.com/",
                     icon: "https://registry.walletconnect.com/v2/logo/md/dee547be-936a-4c92-9e3f-7a2350a62e00",
                     redirectUrlHost: "coin98",
                     supportChains: [.evm],
                     deepLink: "coin98://",
                     scheme: "coin98://"))
    }
}

```

## Give Feedback

You can join our [Discord](https://discord.gg/2y44qr6CR2).
