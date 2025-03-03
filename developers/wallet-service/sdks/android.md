# Android

## Add Wallet Service to Your Android Project

### Prerequisites <a href="#prerequisites" id="prerequisites"></a>

Make sure that your project meets the following requirements:

* Targets API Level 23 (Marshmallow) or higher
* Uses Android 6.0 or higher
* Uses [Jetpack (AndroidX)](https://developer.android.com/jetpack/androidx/migrate?authuser=0)

### Create a Particle Project and App

Before you can add Wallet Service to your Android app, you need to create a Particle project to connect to your Android app. Visit [Particle Dashboard](../../../getting-started/dashboard/) to learn more about Particle projects and apps.

[👉 Sign up/log in and create your project now](https://dashboard.particle.network/#/login)

### Add the Wallet Service SDK to your app <a href="#add-sdks" id="add-sdks"></a>

Declare them in your module (app-level) Gradle file (usually app/build.gradle).

{% tabs %}
{% tab title="Kotlin" %}
```kts
repositories {
    google()
    mavenCentral()
    maven { setUrl("https://jitpack.io") }
    //...
}

dependencies {
    //required 
    modules {
        module("org.bouncycastle:bcprov-jdk15to18") {
            replacedBy("org.bouncycastle:bcprov-jdk15on")
        }
    }
    // pick one of two, 
    implementation 'network.particle:connect-auth-adapter:{latest-version}'
    //implementation 'network.particle:connect-auth-core-adapter:{latest-version}'
    
    // Particle Wallet Core Api
    implementation("network.particle:api-service:${latest_version}")
    // Particle Wallet GUI, you can remove it if custom GUI.
    implementation("network.particle:wallet-service:${latest_version}")
    
    //find the latest version of the sdk:
    //https://search.maven.org/search?q=g:network.particle
    //...
}
```
{% endtab %}

{% tab title="Groovy" %}
```groovy
repositories {
    google()
    mavenCentral()
    maven { url "https://jitpack.io" }
    //...
}

dependencies {
    // Particle Auth Service
    implementation 'network.particle:auth-service${latest_version}'
    // Particle Wallet Api
    implementation 'network.particle:wallet-service-api${latest_version}'
    // Particle Wallet GUI, you can remove it if custom GUI.
    implementation 'network.particle:wallet-service${latest_version}'
    //...
}
```
{% endtab %}
{% endtabs %}

The Wallet Service can only be used after a successful log-in with Connect Service.

{% hint style="info" %}
Wallet Service depends on Connect Service, you must import [Connect Service.](../../connect-service/sdks/android.md)
{% endhint %}

{% hint style="info" %}
If you want to receive release updates, subscribe to our [GitHub repository](https://github.com/Particle-Network).
{% endhint %}

## Wallet Core Reference

### Initialize the SDK

```kotlin
class App : Application() {
    override fun onCreate() {
        super.onCreate()
        //init Particle SDK for solana chain 
        ParticleNetwork.init(this, Env.DEV, ChainInfo.Ethereum)
        
        //init wallet service
        /**
         *  supportChains is optional, if not provided, all chains will be supported
         *  if provided, only the chains in the list will be supported,Only the main chain is required,
         *  if you want to support devnet, you can call showTestNetworks() to show the devnet networks
         */
        val supportChains = mutableListOf(
            ChainInfo.Ethereum,
            ChainInfo.BNBChain,
            ChainInfo.Solana
        )
        ParticleWallet.init(this,supportChains).apply {
            showTestNetworks() // show test networks,default is hide
            hideManageWallet() // hide manage wallet item in setting page,default is show
        }
      
    }
}
```

### Solana Service

#### Get the real-time exchange rate of the Solana token

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
// call enhanced method: enhancedGetPrice
ParticleNetwork.solana.getPrice(addresses, currencies)
```
{% endtab %}
{% endtabs %}

#### Get token list and NFT list by giving an address

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
// call enhanced method: enhancedGetTokensAndNFTs
//fetch from server、save to db and return 
ParticleNetwork.solana.getTokensAndNFTs(address)
//get data from db only
ParticleNetwork.solana.getTokensAndNFTsFromDB(address)
```
{% endtab %}
{% endtabs %}

#### Get parsed transaction history by giving an address

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
// call enhanced method: enhancedGetTransactionsByAddress
ParticleNetwork.solana.getTransactionsByAddress(address, optBody)
//get trans from db
ParticleNetwork.solana.getTransactionsByAddressFromDB(address, optBody)
```
{% endtab %}
{% endtabs %}

#### Serialize unsigned transaction

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
// call enhanced method: enhancedSerializeTransaction
val output = ParticleNetwork.solana.serializeTransaction(txBody)

//send transaction with Particle Auth
ParticleNetwork.signAndSendTransaction(activity,
        output.result.transaction.serialized, callback);
```
{% endtab %}
{% endtabs %}

#### Get Token info list&#x20;

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
// get solana chain all SPL token info
ParticleNetwork.solana.getTokenList()
```
{% endtab %}
{% endtabs %}

#### Access 👉 [any RPC ](../../node-service/solana-api/)

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
// such as getBalance
ParticleNetwork.solana.rpc("getBalance", 
    listOf("8FE27ioQh3T7o22QsYVT5Re8NnHFqmFNbdqwiF3ywuZQ"))
```
{% endtab %}
{% endtabs %}

#### Add Custom Tokens

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
// publicKey is your user public publicKey
val publicKey = ParticleNetwork.getPublicKey()
// tokenAddresses is token contract address
val tokenList = listOf("Fm9rHUTF5v3hwMLbStjZXqNBBoZyGriQaFM6sTFz3K8A")
// if tokens are existed in blockchain, will return them.
// if no tokens existed in blockchain, will return empty list.
ParticleNetwork.solana.addCustomTokens(publicKey, tokenList)
```
{% endtab %}
{% endtabs %}

### EVM Service

#### Get the real-time exchange rate of the EVM token

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
val parmas1 = arrayListOf("native") 
val parmas2 = arrayListOf("usd") 
val rateResultList = ParticleNetwork.evm.getPrice(parmas1, parmas2)
```
{% endtab %}
{% endtabs %}

#### Get token list and NFT list by giving an address

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
// call enhanced method: particle_getTokensAndNFTs
//fetch from server、save to db  return result 
ParticleNetwork.evm.getTokensAndNFTs(address)
//get data from db only
ParticleNetwork.evm.getTokensAndNFTsFromDB(address)
```
{% endtab %}
{% endtabs %}

#### Get parsed transaction history by giving an address

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
// call enhanced method: particle_getTransactionsByAddress
ParticleNetwork.evm.getTransactionsByAddress(address)
//get data from db only
ParticleNetwork.evm.getTransactionsByAddressFromDB(address)
```
{% endtab %}
{% endtabs %}

#### Get suggested GasFees

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
ParticleNetwork.evm.suggestedGasFees()
```
{% endtab %}
{% endtabs %}

#### Get Token info list&#x20;

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
// get any EVM chain all token info
ParticleNetwork.evm.getTokenList()
```
{% endtab %}
{% endtabs %}

#### Access 👉 [any RPC ](../../node-service/evm-chains-api/)

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
// such as eth_estimateGas
ParticleNetwork.evm.rpc("eth_estimateGas",listOf(mapOf("from" to "0xXXX...", "to" to "0xXXX...")))
```
{% endtab %}
{% endtabs %}

#### Read Contract

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
// example for evm read contract
private suspend fun readContract() {
    ParticleNetwork.evm.readContract(
        "0xd000f000aa1f8accbd5815056ea32a54777b2fc4",
        "balanceOf",
        listOf("0xBbc1CA8776EfDeC12C75e218C64e96ce52aC6671")
    )
}
```
{% endtab %}
{% endtabs %}

#### Write Contract

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
// example for evm write contract
// mint 1 NFT in kovan testnet, the from is sender address or user address in general.
// result is a transaciton string which presented in hex format.
private suspend fun writeContract() {
    val params = ContractParams.customAbiEncodeFunctionCall(
        contractAddress = "0xd000f000aa1f8accbd5815056ea32a54777b2fc4",
        methodName = "mint",
        params = listOf("1")
    )
    ParticleNetwork.evm.writeContract(
        "0xd000f000aa1f8accbd5815056ea32a54777b2fc4",
        params
    )
}
```
{% endtab %}
{% endtabs %}

#### Add Custom Tokens

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
// address is your user public address
val address = ParticleNetwork.getAddress()
// tokenAddresses is token contract address
val tokenList = listOf("Fm9rHUTF5v3hwMLbStjZXqNBBoZyGriQaFM6sTFz3K8A")
// if tokens are existed in blockchain, will return them.
// if no tokens existed in blockchain, will return empty list.
ParticleNetwork.evm.addCustomTokens(address, tokenList)
```
{% endtab %}
{% endtabs %}

#### Serialize the data of any contract

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
ParticleNetwork.evm.abiEncodeFunctionCall(params: List<Any>)
ParticleNetwork.evm.abiEncodeFunctionCall(contractAddress: String,method: String,params: List<Any>,json: String = "")

//The above two methods work the same.
// 1. The first parameter is contract address
// 2. The second parameter is the name of the method to be called.
//    Currently, we support:'erc20_transfer'、'erc20_approve'、'erc20_transferFrom'、'erc721_safeTransferFrom'、'erc1155_safeTransferrom'
//    or arbitrary method call: 'custom_{methodName}'
//    for example if you need to call mint method, then pass custom_mint as parameter
// 3. The third parameter is the parameter value array of the method to be called, which should correspond to the above method.
// 4. The fourth parameter is optional，an ABI JOSN string
//    For erc20/721/1155 methods we support, this parameter is not needed
//    And if your contract is verified at the chain's scan website, this parameter is not needed
```


{% endtab %}
{% endtabs %}

#### Serialize the data of stardard contracts

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
// call 'transfer(to, amount)' function in erc20
suspend fun erc20Transfer(){
    val contractAddress =""
    val to=""
    val amount:BigInteger = BigInteger.valueOf(1111)
    ParticleNetwork.evm.erc20Transfer(contractAddress,to,amount)
}
// call 'approve(spender, amount)' function in erc20 
suspend fun erc20Approve() {
    val contractAddress = ""
    val spender = ""
    val amount = BigInteger.valueOf(1111)
    val result: ResponseBody =
        ParticleNetwork.evm.erc20Approve(contractAddress, spender, amount)
}
// call 'transferFrom(from, to, amount)' function in erc20
suspend fun erc20TransferFrom() {
    val contractAddress = ""
    val from = ""
    val to = ""
    val amount = BigInteger.valueOf(1111)
    val result: ResponseBody =
        ParticleNetwork.evm.erc20TransferFrom(contractAddress, from, to, amount)
}
// call 'safeTransferFrom(from, to, tokenId)' function in erc721
suspend fun erc721SafeTransferFrom() {
    val contractAddress = ""
    val from = ""
    val to = ""
    val tokenId = ""
    val result: ResponseBody =
        ParticleNetwork.evm.erc721SafeTransferFrom(contractAddress, from, to, tokenId)
}
// call 'safeTransferFrom(from, to, id, amount, data)' function in erc1155
suspend fun erc1155SafeTransferFrom() {
    val contractAddress = ""
    val from = ""
    val to = ""
    val id = ""
    val amount = BigInteger.valueOf(1111)
    val data: String;
    ParticleNetwork.evm.erc1155SafeTransferFrom(contractAddress, from, to, id, amount, data)
}
```
{% endtab %}
{% endtabs %}

#### Create Transaction

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
/**
 * from: sender address.
 * to: receiver address or contract address.
 * value: (optional) hex of the value sent with this transaction.
 * contractParams: (optional) call contract function params.
 * type: default value "0x2"(EIP1559), "0x1"(EIP2930), "0x0"(Legacy)
 * nonce: default value "0x0", particle auth manage nonce without cancel or speed transaction.
 * gasFeeLevel: default value TransGasFeeMode.medium, transaction gas fee level.
 * action: detault TxAction.normal. if you cancel/speed tracsaction, set the vaule.
 * abiEncodeData: the abi_encodeFunctionCall result, default value is null.
 */
val tx = ParticleNetwork.evm.createTransaction(from, to, value, contractParams,
     type, nonce, gasFeeLevel, action,abiEncodeData)

//send transaction with particle auth
ParticleNetwork.signAndSendTransaction(activity, tx.serialize(), callback)
```
{% endtab %}
{% endtabs %}

you can call contract function by set `contractParams:`

* `ContractParams.erc20Transfer()`
* `ContractParams.erc20Approve()`
* `ContractParams.erc20TransferFrom()`
* `ContractParams.erc721SafeTransferFrom()`
* `ContractParams.erc1155SafeTransferFrom()`
* `ContractParams.customAbiEncodeFunctionCall()`

#### Read Contract

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
// create tx data
val tx = TxData(null, "0x0", null, from, to ,null, data, ParticleNetwork.chainId.toHexString())

//Integer block number, or the string 'latest', 'earliest' or 'pending'
val quantity = "latest"
//call eth_call rpc
val output = ParticleNetwork.evm.rpc("eth_call", listOf(tx, quantity))
```
{% endtab %}
{% endtabs %}

## Wallet UI Reference

### Set pay feature, default support pay feature.

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
// You can disable pay feature in UI, default is true.
ParticleNetwork.enablePay(false)
```
{% endtab %}

{% tab title="Java" %}
```java
ParticleWallet.enablePay(ParticleNetwork.INSTANCE,false);
```
{% endtab %}
{% endtabs %}

### Custom Wallet UI &#x20;

Control show or hide test network, manage wallet page, support chain.

Because ParticleWalletGUI dependent on Particle Connect, Particle Connect initialize chain will add to support chain automatically.&#x20;

```kotlin
// show or hide test network, default is false.
ParticleWallet.showTestNetworks()

// show or hide manage wallet page.
ParticleWallet.hideManageWallet()

// support chain, Particle Connect initialize chain will add as a support chain automatically.
// default support all chains in Particle Network base
ParticleWallet.init(context,supportChains)

//You can initialize the above configuration with a single statement
ParticleWallet.init(
        context,
        supportChains,
    ).apply {
      setShowTestNetworkSetting(true) //default is false
      setShowManageWalletSetting(true) // default is true
      hideMainBackIcon() // hide the back icon in the main page
      setShowAppearanceSetting(true) //default is true
      setShowLanguageSetting(true) //default is true
      setSupportDappBrowser(true) //default is true
      setWalletIcon("https://xxx.xxx/xxx.png") //set the wallet icon
    }
    
//set Wallet Name,only support ParticleConnectAdapter after connect success
val yourWalletName ="X Wallet"
val wallet = WalletInfo.createWallet(
    ParticleNetwork.getAddress(),
    ParticleNetwork.chainInfo.name,
    ParticleNetwork.chainInfo.id,
    1,
    yourWalletName,
    MobileWCWalletName.Particle.name
)
ParticleWallet.setWallet(wallet)

// Set your custom localizable string
// Overlay strings.xml 
YourProject/app/src/main/res/
    values/strings.xml
    values-ja-rJP/strings.xml
    values-ko-rKR/strings.xml
    values-zh-rCN/strings.xml
    values-zh-rHK/strings.xml
    values-zh-rTW/strings.xml
Reassign a value
<string name="pn_network_fee">new NetworkFee</string>
<string name="pn_particle_auth_wallet">New Wallet</string>

```

### Open Wallet

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
PNRouter.build(RouterPath.Wallet).navigation()
```
{% endtab %}

{% tab title="Java" %}
```java
PNRouter.build(RouterPath.Wallet).navigation();
```
{% endtab %}
{% endtabs %}

`navigation()`can add parameters

### Open Send Token

{% tabs %}
{% tab title="Kotiln" %}
```kotlin
//open send spl token
val params = WalletSendParams(tokenAddress, toAddress?, toAmount?)
PNRouter.build(RouterPath.TokenSend, params).navigation()

//open send default token by chain name
PNRouter.build(RouterPath.TokenSend).navigation()
```
{% endtab %}

{% tab title="Java" %}
```java
//open send spl token
WalletSendParams params = new WalletSendParams(tokenAddress, toAddress, toAmount);
PNRouter.build(RouterPath.TokenSend, params).navigation();

//open send default token by chain name
PNRouter.build(RouterPath.TokenSend).navigation();
```
{% endtab %}
{% endtabs %}

`WalletSendParam` has three parameters:

1. `tokenAddress`: token mint address
2. `toAddress`: receiver address (optional)
3. `toAmount`: send token amount, token minimum unit (optional)

### Open Receive Token

Display your address QRCode.

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
PNRouter.build(RouterPath.TokenReceive).navigation()
```
{% endtab %}

{% tab title="Java" %}
```java
PNRouter.build(RouterPath.TokenReceive).navigation();
```
{% endtab %}
{% endtabs %}

### Open Transaction Records

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
//open spl token transaction records
val params = TokenTransactionRecordsParams(tokenAddress)
PNRouter.build(RouterPath.TokenTransactionRecords, params).navigation()

//open default token transaction records by chain name
PNRouter.build(RouterPath.TokenTransactionRecords).navigation()
```
{% endtab %}

{% tab title="Java" %}
```java
//open spl token transaction records
TokenTransactionRecordsParams params = new TokenTransactionRecordsParams(tokenAddress);
PNRouter.build(RouterPath.TokenTransactionRecords, params).navigation();

//open default token transaction records by chain name
PNRouter.build(RouterPath.TokenTransactionRecords).navigation();
```
{% endtab %}
{% endtabs %}

### Open NFT Details

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
var params = NftDetailParams("nftMint")
PNRouter.build(RouterPath.NftDetails, params).navigation()
```
{% endtab %}

{% tab title="Java" %}
```java
 NftDetailParams params = new NftDetailParams("nftMint")
 PNRouter.build(RouterPath.NftDetails, params).navigation()
```
{% endtab %}
{% endtabs %}

### Open Buy Crypto

```kotlin
// // open buy crypto with parameters
// every parameter is optional
ParticleNetwork.openBuy(
    walletAddress: String? = null,
    amount: Int? = null,
    fiatCoin: String = "usd",
    cryptoCoin: String = "eth",
    fixFiatCoin: Boolean = false,
    fixFiatAmt: Boolean = false,
    fixCryptoCoin: Boolean = false,
    theme: String = "light",
    language: String = "en-us",
    chainName: String? = null
)
```

### Open Swap

```kotlin
// open default swap page
PNRouter.navigatorSwap()

// open swap page with from token, to token, and uiAmount, all parameters is optional.
val swapConfig = SwapConfig(
    fromTokenAddress="", 
    toTokenAddress="", 
    fromTokenUIAmount="0.1"
    )
PNRouter.navigatorSwap(swapConfig)

```

### Open dapp browser page

```kotlin
PNRouter.navigatorDappBrowser(url:String)
or
ParticleNetwork.navigatorDAppBrowser(url:String)
```
