---
layout: default
title: IDWise Developers
nav_order: 1
has_children: true
---

# Developers Guide to Integrate With IDWise


There are many ways to integrate with our system, including a mobile SDK, or by sending images of documents through an API:

## Client SDKs:
We strongly recommend using the following Client SDKs to integrate the IDWise KYC capability into your app, we strive to make the integration effort minimal while maintaining a rich and customisable experience:

-   [Android SDK](https://developers.idwise.com/android-sdk.html)
-   [iOS SDK](https://developers.idwise.com/ios-sdk.html)
-   [Web SDK (Javascript)](https://developers.idwise.com/web-sdk.html)

Business ID (Journey Template ID) for the above client SDKs will be provided by the IDWise team.

## Backend API:
This API helps you get information about IDWise journeys from your backend.
Documentation for this API will be sent to you as part of your onboarding on the IDWise platform by the engineering team.

## Anti Money Laundering (AML) Standalone API: 
[AML Check service](https://developers.idwise.com/aml.html) is an API that allows IDWise customers to search for individuals over Anti Money Laundering (AML) datasets which are updated daily, including Politically Exposed Person (PEPs) and global sanctions, and many more lists.



## Document Processing API: 
In case you want to test the ID Verification capability via an API (not via one of the client SDKs above, which we strongly recommend), you can use [this simple API.](https://developers.idwise.com/document-processing-api.html)

This API allows you to send one document (Front side and optionally a backside) as well as a selfie photo, and IDWise will process and return the results (you will not get the benefits of Client SDKs like smart capturing of the document and UI that guides the user through the journey, you will also only be able to process on document as part of the journey if you use this API).

## API Key & Secret:
You will be provided by API Key and API Secret upon your onboarding on the platform.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
## Happy integration!
Our team is more than happy to help answer any questions about the integration or the product, you can reach out via email or WhatsApp.
