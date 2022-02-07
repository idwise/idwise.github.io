---
layout: default
parent: IDWise Developers
title: IDWise Flutter Integration
nav_order: 3
---

# IDWise Smart Onboarding SDK for Flutter


In an increasingly digital world, relying solely on conventional and manual remote validation methods involving database checks, IP addresses and credit bureau checks is cumbersome, inefficient and opens your customers' sensitive data to a variety of data breach security risks. IDWise’s cutting-edge document verification, face match and liveness detection solution establishes a robust layer of trust between your business and your prospective customer so that you can focus your effort on developing your business faster and with more confidence while stopping even the most sophisticated fraudsters in their tracks.

This git repository contains the official IDWiseSDK meant to be used in Flutter applications. This README contains all the needed information to test and integrate the IDWiseSDK in your flutter project.

## Requirements

### iOS ###

The minimum deployment target for IDWiseSDK is iOS 12.0. In order to use the SDK your application minimum deployment target should be iOS 12.0 or higher.
On your development machine you need to have XCode and CocoaPods installed. Both Intel and M1 (Apple Sillicon) based machines are supported. When working with Cocoapods you might have to run some commands through Rosetta Stone compatibility mode.

If you are building iOS project on VSCode and using an M1 machine then It's recommended to use Xcode for iOS builds because VSCode does not support Roestta mode and we need Rosetta option on both terminal and Xcode to run our project without any Issues of linking architecture.
Flutter also has disabled support for builds through VSCode starting from iOS 14, so we need Xcode for iOS builds anyway. 

### Android ###

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

## Usage

Invoking IDWise SDK is very simple. First import IDWise package in your .dart file from where you want to invoke IDWise SDK:

```flutter
import IDWise
```

### Flutter Bridging to native platforms

We need to create a MethodChannel class object with some unique name assigned to channel. To use MethodChannel class, we need to import following dart services to our project.

```flutter
import 'dart:async';
import 'package:flutter/services.dart';
```

We will create object of MethodChannel class in our widget class as a variable. METHOD_CHANNEL_NAME can be any unique string.

```flutter
static const platformChannel = MethodChannel("<METHOD_CHANNEL_NAME>");
```

Now, we will use methodChannel object to invoke our native platform code. Calling the native code is an asynchronous operation so we need to create a method which returns a Future and is an async method like below. This is all the code that we need to do on flutter side and everything else will be handeled in native platform classes.

```flutter
  Future<void> _startSDK() async {
    try {
      /**
       * You can call initialize either in initState() of your Page
       * where you are going to start the verification, to pre-initialize the SDK.
       * Or you can call it along with startJourney(). Which suits best for your
       * usecase. Further implementation is done in MainActivity.kt for Android
       * and AppDelegate.swift for iOS
       */

      platformChannel.invokeMethod('initialize');

      /**
       * You can call the startJourney when you wan to start the verification
       * process. Further implementation is done in MainActivity.kt for Android
       * and AppDelegate.swift for iOS
       */

      platformChannel.invokeMethod('startJourney');

    } on PlatformException catch (e) {
      print("Failed : '${e.message}'.");
    }
  }
```

### Native Code to start Journey (iOS)

IDWise SDK is designed to start on top of a UIViewController in your application. Each user onboarding or verification transaction is named a user journey. In flutter project, we will navigate to projectfolder/ios/Runner and will work in AppDelegate.swift class. In this file, we will write our code in **didFinishLaunchingWithOptions** method. Just before returning from this method, we will add our code.

To start a new journey just provide the UIViewController from which you want the flow to start then call `IDWiseSDK.initialize` method first with your provided client key and then you can call `IDWise.startJourney` method. If initialization is failed for any reason, you will get an error object with a code and a message explaining the reason of the error. In the following example, we called initialize method and then called startJourney method.

As in flutter, we don't have a UIViewController so we will create controller object using FlutterViewController class as done in code below. Then we will create a FluterMethodChannel object with same channel name as we assigned on dart side while creating the channel.
Finally, we will implement channel's handler method and depending on method name called from flutter class end, we will take action and invoke our native methods. As we invoked two methods from flutter class so we are handling for two methods in our AppDelegate.swift file.


```swift
      // Native code bridging Swift -> Dart , calling iOS SDK here
      let controller : FlutterViewController = window?.rootViewController as! FlutterViewController
      let channel = FlutterMethodChannel(name: methodChannelName,
                                                binaryMessenger: controller.binaryMessenger)
      channel.setMethodCallHandler({ [self]
          (call: FlutterMethodCall, result: @escaping FlutterResult) -> Void in
          
          switch call.method {
          case "initialize":
              IDWise.initialize(clientKey: "<YOUR_CLIENT_KEY>") { error in
               result("got some error")
              }

          case "startJourney":
              IDWise.startJourney(journeyDefinitionId: "<YOUR_JOURNEY_DEFINITION_ID>",locale: "en", delegate: self)
              result("successfully started journey")
          default:
              result(FlutterMethodNotImplemented)
          }
          
      })
        
``` 

This will make IDWise SDK show a UI with a wizard to guide the user through completing the onboarding journey

This method takes two parameters:
- `journeyDefinitionId`: Specifies the journey definition (aka template) to base this new journey on. Journey definitions are created based on your requirements and specify what documents and biometrics to collect from the user and in what order. JourneyDefinitionId is shared with you by IDWise team as part of your use-case and requirements discussions.
- `referenceNo` : A custom identifier to associate with this journey to enable you to link it back easily or associate it with a user on your system.
- `locale` : Language code of the language to be used to display the journey user interface. This is either an ISO 639-1 (2-letter for example en) or IETF BCP 47 (2-letter plus extended language specified for example zh-HK or zh-CN)
- `delegate`: This parameter is used to provide a set of event handlers to handle the different events that are triggered from IDWise SDK. These events indicate the lifetime of a journey and provide oppurtunity for your application to react to certain events.

For example we can implement the protocol as an extension on the ViewController like so:

```swift
extension AppDelegate:IDWiseSDKDelegate {
    func onError(errorMessage: IDWiseSDKError) {
       
    }
    
    func JourneyCancelled() {
        
    }
    
    func JourneyStarted(journeyID: String) {
        
    }
    
    func JourneyFinished() {
        
    }
}
```

When the journey is started it is assigned a unique id called Journey ID in IDWise system and this is provided as a parameter, `journeyID` with the triggering of `JourneyStarted` event.
This identifier can be used to fetch the data and status of the journey from IDWise Journey Fetch API at any time.

The steps that compose part of the journey and the prompts that user see are all cutomisable through IDWise cloud system.

### Native Code to start Journey (Android)

## Code Example
Please find the [`following example`](https://github.com/idwise/idwise-flutter-example/tree/main/ios) for an XCode project that showcases the integration with IDWise iOS Framework.
