---
description: Integrate powerful Web3.0 Unity SDK in minutes
---

# Unity SDK Prerequisites

### Prerequisites <a href="#prerequisites" id="prerequisites"></a>

* Install Unity 2020.3.26f1 or later. Earlier versions may also be compatible but will not be actively supported.&#x20;
* _(iOS only)_ Install the following:
  * Xcode 14.1 or higher
  * CocoaPods 1.11.0 or higher
* Make sure that your Unity project meets these requirements:
  * **For iOS** — targets iOS 14 or higher
  * **For Android** — Minimum API Level 23 or higher, Targets API level 33 or higher, Pack apk must be with exporting project to Android Studio, [change Java SDK version to 11](https://stackoverflow.com/questions/66449161/how-to-upgrade-an-android-project-to-java-11)

### Create a Particle Project and App

Before you can add our Auth Service to your Unity game, you need to create a Particle project to connect to your iOS and Android app. Visit [Particle Dashboard](../../dashboard/) to learn more about Particle projects and apps.

[👉 Sign up/log in and create your project now](https://dashboard.particle.network/#/login)

### Import Unity package

#### Download and import the Unity package

[https://github.com/Particle-Network/particle-unity/releases](https://github.com/Particle-Network/particle-unity/releases)

### **iOS**

* **Configure scheme url in Unity Editor**

1. Open the [iOS Player Settings](https://docs.unity3d.com/Manual/class-PlayerSettingsiOS.html) window (menu: **Edit** > **Project Settings** > **Player Settings**, then select **iOS**).
2. Select **Other**, then scroll down to **Configuration**.
3. Expand the **Supported URL schemes** section and, in the **Element 0** field, enter the URL scheme to associate with your application. For example, if your project app id is "63bfa427-cf5f-4742-9ff1-e8f5a1b9828f", your scheme URL is "pn63bfa427-cf5f-4742-9ff1-e8f5a1b9828f"

* **Remove other service code if you don't need them.**

1. In ParticleNetworkIOSBridge.cs, there are 5 part ParticleNetworkBase, ParticleAuthService, ParticleWalletAPI, ParticleWalletGUI, ParticleConnect, works for interact with iOS native. ParticleNetworkBase is required,&#x20;
2. ParticleAuthService if required for Auth Service,&#x20;
3. ParticleConnect is required for Connect Service.
4. You can remove other codes if you don't need them.

* **Configure Xcode project after iOS build.**

1.  Create a Podfile if you don't already have one. From the root of your project directory, run the following command:

    ```ruby
    pod init
    ```
2.  To your Podfile, add select these pods that you want to use in your app:

    ```ruby
    // ParticleWalletGUI contains all other services.
    pod 'ParticleWalletGUI'
    // ParticleAuthService provide auth service.
    pod 'ParticleAuthService'
    // PaticleConnectService privide wallet connect and auth service.
    pod 'ParticleConnect'
    pod 'CommonConnect'
    ```
3.  &#x20;Install the pods, then open your `.xcworkspace` file to see the project in Xcode:

    ```ruby
    pod install --repo-update
    ```

    ```ruby
    open your-project.xcworkspace
    ```

*   Edit Podfile

    {% hint style="info" %}
    ### Edit Podfile

    ```ruby
    // From 0.9.12, you should add more in podfile
    If you use ParticleWalletGUI, you need add this one.
    pod 'SkeletonView', :git => 'https://github.com/SunZhiC/SkeletonView.git', :branch => 'main'

    // If you use PartcleWalletConnect or ConenctWalletConnectAdapter, you need add this one.
    pod 'WalletConnectSwift', :git => 'https://github.com/SunZhiC/WalletConnectSwift', :branch => 'master'

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


* **Configure Project information.**

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

### **Android**&#x20;

#### Configuration file path is  Assets/Plugins/Android/gradleTemplate.properties

```
particle.network.project_client_key=Your Project Client Key
particle.network.project_id=Your Project Client ID
particle.network.app_id=Your App ID

//like this
particle.network.project_client_key=cKtHdeHis0ghNom64w7mdNkGYX5rmRr0jLlIKatY
particle.network.project_id=fa0fe0e8-f1bb-47ff-88bb-4fa31711e7b3
particle.network.app_id=859fada8-48ad-441b-b8e6-cde15ae1b48f

```



### Android, iOS Native Service Config <a href="#add-sdks" id="add-sdks"></a>

**Android** — Configuration file path is  Assets/Plugins/Android/launcherTemplate.gradle

```
dependencies {
    implementation project(':unityLibrary')
    def sdkVersion = "0.4.1" //find the latest version of the sdk here:https://search.maven.org/search?q=g:network.particle
    //particle auth service (Required)  
    implementation "network.particle:auth-service:${sdkVersion}"
    //particle api service (Optional.) If you don't want to use the API service, you can remove this dependency.
    implementation "network.particle:api-service:${sdkVersion}"
    //particle wallet service (Optional.) If you don't want to use the wallet service, you can remove this dependency.
    implementation "network.particle:wallet-service:${sdkVersion}"
    //particle unity bridge (Required) 
    implementation "network.particle:unity-bridge:${sdkVersion}"
}
```

**iOS**&#x20;

1. Download `UnityManger.swift`, `Unity-iPhone-Bridging-Header.h` and `AppDelegate.swift` from under github `/Assets/Plugins/iOS/.Swift` , Copy files into the root of your Xcode project. Xcode will ask you if auto create Bridging file, click yes.

![](<../../../.gitbook/assets/image (2) (1) (1).png>)

2.Make sure Build Settings, Swift Compiler - General, has Objective-C Bridging Header, its connect is Unity-iPhone-Bridging-Header.h 's local path.

<figure><img src="../../../.gitbook/assets/image (5) (2).png" alt=""><figcaption></figcaption></figure>

3\. Remove `main.mm` under MainApp folder.

4\. Under `/Assets/Plugins/iOS` is NativeCallProxy files, they are requested by Unity to interact with iOS code. Remove code under Particle Wallet API and Particle Wallet GUI if you don't need wallet service.

5\. In `UnityManger.swift`, it has implemented methods defined in `NativeCallProxy.h`

6\. Select NativeCallProxy.h, in the file inspector, check public in Target Membership.

![](<../../../.gitbook/assets/image (3) (1) (2).png>)

7\. If you want to use ParticleConnect, you should add LSApplicationQueriesSchemes to info.plist.

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

8\. If you are skilled in iOS, you can modify these files as you like. For example, add other services.

### FAQ

### Unity Editor

1. In demo, after login tap Sign And Send Transaction encounter failed or error?

In demo, we create a test transaction, that send some tokens in Ethereum Goerli Testnet, after you login from particle auth, other wallet or private key, your account may not have enough tokens.

you could get some test tokens from out test account, in file`TestAccount.cs` , we provide private keys for you to test in demo.

or get test tokens from the following URL

&#x20;[https://faucets.chain.link/](https://faucets.chain.link/) or [https://goerlifaucet.com/](https://goerlifaucet.com/)

Before sign and send transaction, make sure you have enough token to finish the transaction.

### iOS

1. After export to Xcode project, when you encounter error ''Module complied with Swift 5.6.1 cannot be imported by the Swift 5.7 complier'' ?

Firstly, check the Podfile, do you add the code in Edit Podfile in Prerequistites.

Secondly, check the Podfile, do you add the pods to under right target.

more pod version information is here.

&#x20;[https://github.com/Particle-Network/particle-ios](https://github.com/Particle-Network/particle-ios)

&#x20;[https://github.com/Particle-Network/particle-connect-ios](https://github.com/Particle-Network/particle-connect-ios)

Here is an example in Podfile.  All particle pods should under target 'Unity-iPhone' not target 'UnityFramework’.

```
platform :ios, '13.0'
target 'Unity-iPhone' do
  source 'https://github.com/CocoaPods/Specs.git'
  use_frameworks!
  
pod 'ParticleWalletGUI', '0.13.0’
pod 'ParticleAuthService', '0.12.0’
pod 'ParticleWalletAPI', '0.12.0’
pod 'ParticleNetworkBase', '0.12.0’
pod 'ParticleWalletConnect', '0.12.0'
pod 'ConnectWalletConnectAdapter', '0.1.52’
pod 'ConnectPhantomAdapter','0.1.52’
pod 'ConnectEVMAdapter', '0.1.52’
pod 'ConnectSolanaAdapter', '0.1.52’
pod 'ParticleConnect', '0.1.52’
pod 'ConnectCommon', '0.1.52’

end

target 'UnityFramework' do
  use_frameworks!
end

post_install do |installer|
installer.pods_project.targets.each do |target|
  target.build_configurations.each do |config|
  config.build_settings['BUILD_LIBRARY_FOR_DISTRIBUTION'] = 'YES'
    end
  end
 end
```

2\. After export to Xcode project, when you encounter error ''Cannot find type 'NativeCallsProtocol'.h in scope''.

Make sure you have changed NativaProxy.h from project to public, select this file, then in the right area, you can see a Target Membership, in iOS step-5.

3\. When execute pod install in Terminal, encounter error “Unable to determine Swift version for the following pods”

Remove particle pods in podfile, then execute pod install, open Unity-iPhone.xcworkspace, select Unity-iPhone under TARGETS, in Building Settings, Swift Complier-Language, set swift version to swift 5, if there is no Swift Complier section, you should create an empty swift file,  you can see swift version after do that.

\


### Android
