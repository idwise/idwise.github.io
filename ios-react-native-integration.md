---
layout: default
parent: React Native Integration
grand_parent: IDWise Developers
title: iOS Simple Journey
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

This git repository contains the official IDWiseSDK meant to be used in React-Native applications. This README contains all the needed information to test and integrate the IDWiseSDK in your React-Native project.

{% include cta.md %}

## Xcode Supported Version

The current latest release of IDWise SDK is IDWise 4.2.3

|  Xcode    | SDK    |  
| -------   | ------ |
| 14.3.1    | 4.2.3  |

## Requirements

The minimum deployment target for IDWiseSDK is iOS 12.0. In order to use the SDK your application minimum deployment target should be iOS 12.0 or higher.
On your development machine you need to have XCode and CocoaPods installed. Both Intel and M1 (Apple Sillicon) based machines are supported. When working with Cocoapods you might have to run some commands through Rosetta Stone compatibility mode.

## Installation

IDWiseSDK is available to install via [CocoaPods package manager](https://cocoapods.org) from IDWise private Cocoapods repository. 

To add IDWise SDK to your project, first ensure you have these two lines at the top of your Podfile file:

```ruby
source 'https://cdn.cocoapods.org/'
source 'https://github.com/idwise/ios-sdk'
```
Next add this line also to your Podfile but this time underneath your `target` node for your project:

```ruby
pod 'IDWise'
```

Also, add this configuration underneath your `target` node for your project:

```ruby
  post_install do |installer|
    installer.pods_project.build_configurations.each do |config|
      config.build_settings['CODE_SIGNING_ALLOWED'] = 'NO'
      config.build_settings["EXCLUDED_ARCHS[sdk=iphonesimulator*]"] = "arm64"

    end
  end
```

After adding our dependency in your Podfile run:

```
pod install
```

### Linking ###

In order to Link the React Code with iOS, we need to perform some steps.

1. We need to create a swift class with objective-c bridging, let's call it `IDWiseModule`. You can find the sample [`IDWiseModule.swift`] (<https://github.com/idwise/idwise-react-native-sdk-samples/blob/main/IDWiseSimpleJourney/ios/IDWiseModule.swift>) for sample code.This class will act as a bridge between swift and react native, because react native's ios bridging code is written in objectve-c so we have to mark our swift class with `@objc` annotation.

2. We need to create a header `(.h)` and an implementation file `(.m)` for our swift class with same name as our swift class. In header file, we do some native module imports and in implementation file we export our swift class and It's method's as module and method's respectively. You can find the sample of both of these files [`IDWiseModule.h`,`IDWiseModule.h`] at (<https://github.com/idwise/idwise-react-native-sdk-samples/blob/main/IDWiseSimpleJourney/ios/IDWiseModule.h>) and (<https://github.com/idwise/idwise-react-native-sdk-samples/blob/main/IDWiseSimpleJourney/ios/IDWiseModule.m>) respectively.

## React-Native Usage

Invoking IDWise SDK is very simple. First reterieve `IDWiseModule` from `NativeModules` in your .js file from where you want to invoke IDWise SDK:

```javascript
const { IDWiseModule } = NativeModules;
```

### Initializing the SDK

In order to use the SDK, we need to initialize it first with the `<CLIENT_KEY>` provided by IDWise. You can pre-load this on `componentDidMount()` if you want to. Here is how we can initialize the SDK.

```javascript
const theme = 'SYSTEM_DEFAULT'; //[ LIGHT, DARK, SYSTEM_DEFAULT ]
IDWiseModule.initialize("<CLIENT_KEY>", theme);
```

### Starting the Journey

Now we can start the verfication journey by calling `startJourney(..)` of the SDK like below. This will start the verification process and that's it.

```javascript
IDWiseModule.startJourney("<JOURNEY_DEFINITION_ID>","<REFERENCE_NO>","<LOCALE>");
```

### Events (Callbacks)

Throughout the journey, IDWise SDK sends back some events to your app. Here are examples how to listen to these events:

These are the supported events:

* **onJourneyStarted:**  Triggered when the journey is started and the Journey ID is assigned.
* **onJourneyFinished:**  Triggered when the journey is completed by the user.
* **onJourneyCancelled:**  Triggered when the user cancels the journey and doesn't finish it.
* **onJourneyResumed:**  Triggered when an existing journey is resumed.
* **onError:**  Triggered when an error occurs for example a network connectivity error and lack of permission to access the camera.

<br />

```javascript
useEffect(() => {
  const eventEmitter = new NativeEventEmitter(NativeModules.IDWiseModule);

  eventEmitter.addListener("onError", (event) => {
    console.log(
      `An Error has occured  ${event.errorCode} : ${event.errorMessage}`
    );
  });

  eventEmitter.addListener("onJourneyStarted", (event) => {
    console.log(`Journey Started with id ${event.journeyId}`);
  });

  eventEmitter.addListener('onJourneyResumed', event => {
    console.log(`Journey Resumed with id ${event.journeyId}`);
  });

  eventEmitter.addListener("onJourneyFinished", (event) => {
    console.log(`Journey Completed with id ${event.journeyId}`);
  });

  eventEmitter.addListener("onJourneyCancelled", (event) => {
    console.log(`Journey Cancelled with id ${event.journeyId}`);
  });
}, []);
```

## Sample Project

You can find the [`Sample Project`](https://github.com/idwise/idwise-react-native-sdk-samples/tree/main/IDWiseSimpleJourney) for sample code for React-Native Integration.
