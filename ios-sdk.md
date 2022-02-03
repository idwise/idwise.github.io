---
layout: default
parent: IDWise Developers
title: iOS SDK
nav_order: 3
---

# IDWise Smart Onboarding SDK for iOS
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

This git repository contains the official IDWiseSDK meant to be used in iOS applications. This README contains all the needed information to test and integrate the IDWiseSDK in your project.

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

Invoking IDWise SDK is very simple. First import IDWise package in your code file:

```swift
import IDWise
```

### Starting a user journey

IDWise SDK is designed to start on top of a UIViewController in your application. Each user onboarding or verification transaction is named a user journey.

To start a new journey just provide the UIViewController from which you want the flow to start then call `IDWiseSDK.initialize` method first with your provided client key and then you can call `IDWise.startJourney` method. If initialization is failed due to any reason then you will get an error object with a code and a message explaining about reason of error.In following example, we called initialize method and then called startJourney method.

```swift
        IDWise.initialize(clientKey: "<YOUR_CLIENT_KEY>") { error in
                // Deal with error here
                print(error?.message)
        }
        
        IDWise.startJourney(journeyDefinitionId: "<YOUR_CUSTOMER_ID>", referenceNumber: "<YOUR_REFERENCE_NO>", locale: "en", delegate: self)
        
``` 

This will make IDWise SDK show a UI with a wizard to guide the user through completing the onboarding journey

This method takes two parameters:
- `journeyDefinitionId`: Specifies the journey definition (aka template) to base this new journey on. Journey definitions are created based on your requirements and specify what documents and biometrics to collect from the user and in what order. JourneyDefinitionId is shared with you by IDWise team as part of your use-case and requirements discussions.
- `referenceNo` : A custom identifier to associate with this journey to enable you to link it back easily or associate it with a user on your system.
- `locale` : Language code of the language to be used to display the journey user interface. This is either an ISO 639-1 (2-letter for example en) or IETF BCP 47 (2-letter plus extended language specified for example zh-HK or zh-CN)
- `delegate`: This parameter is used to provide a set of event handlers to handle the different events that are triggered from IDWise SDK. These events indicate the lifetime of a journey and provide oppurtunity for your application to react to certain events.

For example we can implement the protocol as an extension on the ViewController like so:

```swift
extension ViewController:IDWiseSDKDelegate {
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


## Code Example
Please find the [`following example`](https://github.com/idwise/idwise-ios-sdk-documentation/tree/main/IDWiseExample) for an XCode project that showcases the integration with IDWise iOS Framework.
