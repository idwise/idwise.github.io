---
layout: default
parent: IDWise Developers
title: React-Native Integration
nav_order: 4.6
heading_anchors: true
---
# IDWise Smart Onboarding SDK for React-Native
{: .no_toc }
<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---


In an increasingly digital world, relying solely on conventional and manual remote validation methods involving database checks, IP addresses and credit bureau checks is cumbersome, inefficient and opens your customers' sensitive data to a variety of data breach security risks. IDWise’s cutting-edge document verification, face match and liveness detection solution establishes a robust layer of trust between your business and your prospective customer so that you can focus your effort on developing your business faster and with more confidence while stopping even the most sophisticated fraudsters in their tracks.

This git repository contains the official IDWiseSDK meant to be used in React-Native applications. This README contains all the needed information to test and integrate the IDWiseSDK in your React-Native project.

## Requirements

### iOS ###

The minimum deployment target for IDWiseSDK is iOS 12.0. In order to use the SDK your application minimum deployment target should be iOS 12.0 or higher.
On your development machine you need to have XCode and CocoaPods installed. Both Intel and M1 (Apple Sillicon) based machines are supported. When working with Cocoapods you might have to run some commands through Rosetta Stone compatibility mode.

If you are building iOS project on VSCode and using an M1 machine then It's recommended to use Xcode for iOS builds because VSCode does not support Roestta mode and we need Rosetta option on both terminal and Xcode to run our project without any Issues of linking architecture.
React-Native also has disabled support for builds through VSCode starting from iOS 14, so we need Xcode for iOS builds anyway. 

### Android ###

The `minSdkVersion` is 16 and `targetSdkVersion` is set to 31 in IDWise Android SDK. It is recommended to install the Latest Android Studio on your development machine. 

## iOS Installation

To install pods in your flutter project, first go to projectfolder/ios. You can create a podfile If does not exist already by running command "pod init" on terminal by going into this ios directory. If you already have a podfile, you can follow following steps.

IDWiseSDK is available to install via [CocoaPods package manager](https://cocoapods.org) from IDWise private Cocoapods repository.
To add IDWise SDK to your project, first ensure you have these two lines at the top of your Podfile file:

```ruby
source 'https://cdn.cocoapods.org/'
source 'https://github.com/idwise/ios-sdk'
```

This adds IDWise private Cocoapods repository as a source to install packages from

Next add this line also to your Podfile but this time underneath your `target` node for your project:

```ruby
pod 'IDWise'
```

You can have a look at the example `Podfile` provided in the root of this repository to see an example `Podfile` with both the changes above completed

After adding our dependency in your Podfile run: 

```
pod install
```
## Android Installation
Add the following in your app level `build.gradle` file located at `projectRoot/android/app/build.gradle`

```
  android {
    ...
    defaultConfig {
      ...
      multiDexEnabled true
    }
    buildFeatures {
      ...
      dataBinding true
    }
  }
```

Add following repositories in you project-level `build.gradle` file located at `projectRoot/android/build.gradle`
```
repositories {
	maven { 
		url 'http://mobile-sdk.idwise.ai/releases/' 
            	allowInsecureProtocol = true
	}
	maven { url 'https://jitpack.io' }
}
```

Finally, Add the following dependency in your app level `build.gradle` file located at `projectRoot/android/app/build.gradle`.
Replace the `x.y.z` with the latest IDWise SDK version

```
dependencies {
	...
	implementation 'com.idwise:android-sdk:x.y.z'
}
```
### Android Linking ###

In order to Link the React Code with android, we need to create a Module, let's call it `IDWiseModule`. You can find the sample [`IDWiseModule.java`](https://github.com/idwise/idwise-react-native-example/blob/main/android/app/src/main/java/com/idwisereactnativesample/IDWiseModule.java) for sample code.

In order to use `IDWiseModule` we need to create a React Package, let's call it `IDWisePackage`. You can find the sample [`IDWisePackage.java`](https://github.com/idwise/idwise-react-native-example/blob/main/android/app/src/main/java/com/idwisereactnativesample/IDWisePackage.java) for sample code.

Now we can add our newly created `IDWisePackage` to our packages, to do this, we need to edit the `MainApplication.java`, located at `projectRoot/android/your/package/name/../MainApplication.java`. In `getPackages()` method, we need to add our Package like below. You can find the sample [`MainApplication.java`](https://github.com/idwise/idwise-react-native-example/blob/main/android/app/src/main/java/com/idwisereactnativesample/MainApplication.java) for sample code.

```java
	@Override
        protected List<ReactPackage> getPackages() {
          List<ReactPackage> packages = new PackageList(this).getPackages();
	  
	  //add IDWisePackage here
           packages.add(new IDWisePackage());
	   
          return packages;
        }
```


## React-Native Usage

Invoking IDWise SDK is very simple. First reterieve `IDWiseModule` from `NativeModules` in your .js file from where you want to invoke IDWise SDK:

```javascript
const { IDWiseModule } = NativeModules;
```

### Initializing the SDK

In order to use the SDK, we need to initialize it first with the `<CLIENT_KEY>` provided by IDWise. You can pre-load this on `componentDidMount()` if you want to. Here is how we can initialize the SDK.

```javascript
IDWiseModule.initialize("<CLIENT_KEY>");
```

### Starting the Journey
Now we can start the verfication journey by calling `startJourney(..)` of the SDK like below. This will start the verification process and that's it.

```javascript
IDWiseModule.startJourney("<JOURNEY_DEFINITION_ID>","<REFERENCE_NO>","en");
```

### Events (Callbacks)
Throughout the journey, IDWise SDK sends back some events to the Hosting app. Here we can listen to those events:

```javascript
useEffect(() => {

    const eventEmitter = new NativeEventEmitter(NativeModules.IDWiseModule);

    eventEmitter.addListener('onError', (event) => {
      console.log(`An Error has occured  ${event.errorCode} : ${event.errorMessage}`); 
    });

    eventEmitter.addListener('journeyStarted', (event) => {
        console.log(`Journey Started with id ${event.journeyId}`); 
    });

    eventEmitter.addListener('journeyCompleted', (event) => {
      console.log(`Journey Completed with id ${event.journeyId} & isSuccess ${event.isSuccess}`);
    });

    eventEmitter.addListener('journeyCancelled', (event) => {
      console.log(`Journey Cancelled with id ${event.journeyId}`); 
    });


  })
```

## Sample Project
You can find the [`Sample Project`](https://github.com/idwise/idwise-react-native-example) for sample code for React-Native Integration.
