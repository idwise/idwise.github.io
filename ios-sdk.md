---
layout: default
parent: IDWise Developers
title: iOS SDK
has_children: true
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

This git repository contains the official IDWise SDK meant to be used in iOS applications. This README contains all the needed information to test and integrate the IDWise SDK in your project.

{% include cta.md %}

## Xcode Supported Version

IDWise SDK always supports latest Xcode version only. The current latest release of IDWise SDK supports below Xcode versions.

|  Xcode  | SDK    |  
| ------- | ------ |
| 14.3    | 4.1.0  |

## Integration Video

Get integrated quickly by watching a 4 minutes video:
{% include youtube.html id='SuLtp3y-EME' %}

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

Also, add this configuration underneath your `target` node for your project:

```ruby
  post_install do |installer|
    installer.pods_project.build_configurations.each do |config|
      config.build_settings['CODE_SIGNING_ALLOWED'] = 'NO'
    end
  end
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

To start a new journey just provide the UIViewController from which you want the flow to start then call `IDWiseSDK.initialize` method first with your provided client key and then you can call `IDWise.startJourney` method. If initialization is failed for any reason, you will get an error object with a code and a message explaining the reason of the error. In the following example, we called initialize method and then called startJourney method.

The IDWiseSDK.initialize method accepts an optional parameter for specifying the theme (dark or light). If this parameter is not passed, the default theme will be the same as that of the operating system settings. If you want the SDK to be in the same theme mode as the OS, you do not need to pass this parameter. However, if the OS is in dark mode and you want the SDK to be in light mode (or vice versa), you can pass the appropriate value for the theme parameter.

Possible values for the theme parameter include "dark" or "light".

```swift

        IDWise.initialize(clientKey: "<YOUR_CLIENT_KEY>",theme: .light) { err in
                // Deal with error here
            if let error = err {
              // handle error, show some alert or any other logic
            }
        }
        
IDWiseSDKTheme.light // to specify light theme mode for SDK
IDWiseSDKTheme.dark // to specify dark theme mode for SDK
IDWiseSDKTheme.systemDefault // to specify the same theme as of operating system

```

```swift
        IDWise.initialize(clientKey: "<YOUR_CLIENT_KEY>") { err in
                // Deal with error here
            if let error = err {
              // handle error, show some alert or any other logic
            }
        }
        
        IDWise.startJourney(journeyDefinitionId: "<YOUR_CUSTOMER_ID>", referenceNumber: "<YOUR_REFERENCE_NO>", locale: "en", journeyDelegate: self)
        
```

This will make IDWise SDK show a UI with a wizard to guide the user through completing the onboarding journey

`IDWise.startJourney` method takes two parameters:

- `journeyDefinitionId`: Specifies the journey definition (aka template) to base this new journey on. Journey definitions are created based on your requirements and specify what documents and biometrics to collect from the user and in what order. JourneyDefinitionId is shared with you by IDWise team as part of your use-case and requirements discussions.
- `referenceNo` : A custom identifier to associate with this journey to enable you to link it back easily or associate it with a user on your system.
- `locale` : Language code of the language to be used to display the journey user interface. This is either an ISO 639-1 (2-letter for example en) or IETF BCP 47 (2-letter plus extended language specified for example zh-HK or zh-CN)
- `journeyDelegate`: This parameter is used to provide a set of event handlers to handle the different events that are triggered from IDWise SDK. These events indicate the lifetime of a journey and provide oppurtunity for your application to react to certain events.

For example we can implement the protocol 'IDWiseSDKJourneyDelegate' as an extension on the ViewController like so:

```swift
extension ViewController:IDWiseSDKJourneyDelegate {
    func onError(error: IDWiseSDKError) {
       
    }
    
    func JourneyCancelled() {
        
    }
    
    func JourneyStarted(journeyID: String) {
        
    }

    func onJourneyResumed(journeyID: String) {

    }
    
    func JourneyFinished() {
        
    }
}
```

When the journey is started it is assigned a unique id called Journey ID in IDWise system and this is provided as a parameter, `journeyID` with the triggering of `JourneyStarted` event.
This identifier can be used to fetch the data and status of the journey from IDWise Journey Fetch API once you get the [webhook call](https://idwi.se/webhooks) to your backend.

The steps that compose part of the journey and the prompts that user see are all cutomisable through IDWise cloud system.

## Code Example

Please find the [`following example`](https://github.com/idwise/idwise-ios-sdk-documentation/tree/main/IDWiseExample) for an XCode project that showcases the integration with IDWise iOS Framework.

## Advanced Dynamic Journey Mode

In some scenarios, your use case might require more flexibility and control over the user journey flow and full control over the user interface / UX above and beyond the wide range of customisations we offer and more than the above sequential flow which simplifies integration, we are introducing: the `Advanced Dynamic Journey` mode, to both of our Android and iOS SDKs to cover some edge advanced use-cases.

From experience, the above simple integration should cover the vast majority of use cases, and allow for a good degree of customisation for the steps and for the UI/UX from the IDWise backend (which applies instantly without the need to update the host app).

If you believe that the simple integration still doesn't cover your use-case, you can find the documentation for this new mode here: [`Advanced Dynamic Journey`](https://idwi.se/ios-advanced)
