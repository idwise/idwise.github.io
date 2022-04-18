---
layout: default
parent: React Native Integration
grand_parent: IDWise Developers
title: iOS
nav_order: 1
heading_anchors: true
---
# IDWise Smart Onboarding iOS SDK for React-Native
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

The minimum deployment target for IDWiseSDK is iOS 12.0. In order to use the SDK your application minimum deployment target should be iOS 12.0 or higher.
On your development machine you need to have XCode and CocoaPods installed. Both Intel and M1 (Apple Sillicon) based machines are supported. When working with Cocoapods you might have to run some commands through Rosetta Stone compatibility mode.


## Installation
IDWiseSDK is available to install via [CocoaPods package manager](https://cocoapods.org) from IDWise private Cocoapods repository. IDWise is a dynamic framework and requires the podfile to use `use_frameworks!` directive to be used so dynamic pods/frameworks can install and run successfully.

React Native’s native iOS dependencies are compiled as static libraries and many react-native node modules are only ever compiled and tested as static libraries. When trying to incorporate a dynamic framework (which Swift dependencies are often bundled as), compilation problems often ensue.

As aforementioned, Cocoapods builds pods by default as static libraries. To use a framework like this we normally use the `use_frameworks!` Cocoapods directive at the top of our Podfile. However, this causes ALL pods to be compiled as dynamic frameworks. Not only will some React Native pods fail, but a large number of 3rd party React Native libraries will as well.

If you can find a way where you can use `use_frameworks!` directive and still able to install `IDWise` pod and other React Native native iOS dependecies (Flipper, Folly, YogaKit etc) and successfully run the react native project then It's fine. Otherwise, the workaround is to use a plugin over cocoapods which can dynamically install specific libraries as static or dynamic during pod installation process.

You can use this plugin to install pods `https://github.com/joncardasis/cocoapods-user-defined-build-types` which enables us to dictate the specific build type of a pod ( dynamic in our case ). You can go through the README section of this plugin as well.
We can easily install this plugin by typing this command in our terminal `gem install cocoapods-user-defined-build-types`. It will install this plugin over our cocoapods. You can use our sample project's podfile in which we used this plugin for `IDWise` pod's dynamic installation.

### Without cocoapods-user-defined-build-types Plugin:
To add IDWise SDK to your project, first ensure you have these two lines at the top of your Podfile file:

```ruby
source 'https://cdn.cocoapods.org/'
source 'https://github.com/idwise/ios-sdk'
```

In this case, you should use `use_frameworks!` directive in your podfile.

This adds IDWise private Cocoapods repository as a source to install packages from

Next add this line also to your Podfile but this time underneath your `target` node for your project:

```ruby
pod 'IDWise'
```


After adding our dependency in your Podfile run: 

```
pod install
```

### With cocoapods-user-defined-build-types Plugin:
To add IDWise SDK to your project, first ensure you have these two lines at the top of your Podfile file:

```ruby
source 'https://cdn.cocoapods.org/'
source 'https://github.com/idwise/ios-sdk'
```

```ruby
plugin 'cocoapods-user-defined-build-types', {
  verbose: true
}
enable_user_defined_build_types!
```

This adds IDWise private Cocoapods repository as a source to install packages from

Next add this line also to your Podfile but this time underneath your `target` node for your project:

```ruby
 pod 'IDWise', :build_type => :dynamic_framework
```

You can have a look at the example `Podfile` provided in the ios folder of this repository to see an example `Podfile` with both the changes above completed

After adding our dependency in your Podfile run: 

```
pod install
```

### Linking ###

In order to Link the React Code with iOS, we need to create a swift class with objective-c bridging, let's call it `IDWiseModule`. You can find the sample [`IDWiseModule.swift`] (<https://github.com/idwise/idwise-react-native-example/blob/main/ios/IDWiseModule.swift>) for sample code.This class will act as a bridge between swift and react native, because react native's ios bridging code is written in objectve-c so we have to mark our swift class with `@objc` annotation.

We also need to create a header `(.h)` and an implementation file `(.m)` for our swift class with same name as our swift class. In header file, we do some native module imports and in implementation file we export our swift class and It's method's as module and method's respectively. You can find the sample of both of these files [`IDWiseModule.h`,`IDWiseModule.h`] at (<https://github.com/idwise/idwise-react-native-example/blob/main/ios/IDWiseModule.h>) and (<https://github.com/idwise/idwise-react-native-example/blob/main/ios/IDWiseModule.m>) respectively.

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
IDWiseModule.startJourney("<JOURNEY_DEFINITION_ID>","<REFERENCE_NO>","<LOCALE>");
```

### Events (Callbacks)
Throughout the journey, IDWise SDK sends back some events to the Hosting app. Here we can listen to those events:

```javascript

     if (Platform.OS === 'ios') {
      const eventEmitter = new NativeEventEmitter(IDWiseModule);

      eventEmitter.addListener('journeyStarted', event =>
        console.log(`Journey Started with id ${event.journeyId}`),
      );

      eventEmitter.addListener('journeyCancelled', event =>
      console.log(`Journey Cancelled with id ${event.journeyId}`)
    );

    eventEmitter.addListener('onError', (event) => {
      console.log(`An Error has occured  ${event.errorCode} : ${event.errorMessage}`); 
    });

    eventEmitter.addListener('journeyCompleted', (event) => {
      console.log(`Journey Completed with id ${event.journeyId}`);
    });

     } 
     
```

## Sample Project
You can find the [`Sample Project`](https://github.com/idwise/idwise-react-native-example) for sample code for React-Native Integration.
