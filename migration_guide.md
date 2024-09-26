---
layout: default
parent: Web SDK
grand_parent: IDWise Developers
title: V2.0 Migration Guide
nav_order: 1
---

# Migration Guide (version 1 to version 4)

This guide will help you to migrate from version 1 to version 4 of the IDWise SDK.

## Recommendation

We strongly recommend using the most recent version of our web SDK by referencing the assets (JS and CSS) from our
servers rather than using a local copy. This ensures you always have access to the latest improvements, bug fixes, and
security updates. By referencing the files directly from our servers, you can be sure that you are using the most
current version of the SDK.

## Steps

Please follow these steps to migrate to the latest version of the SDK:

1. Update the reference to the style sheet file “https://app.idwise.ai/idwise.min.css”.

   **Replace**
    ```
    <link href="https://app.idwise.ai/idwise.min.css" rel="stylesheet">
    ```

   **With**
    ```
    <link href="https://releases.idwise.com/websdk/latest/idwise.min.css" rel="stylesheet">
    ```

2. Update the reference to the script file “https://app.idwise.ai/idwise.min.js”.

   **Replace**
    ```
    <script src="https://app.idwise.ai/idwise.min.js"></script>
    ```

   **With**
    ```
    <script src="https://releases.idwise.com/websdk/latest/idwise.min.js"></script>
    ```

## Deprecation Note

1. `businessId` parameter is deprecated and will be removed in a future release. Please use `clientKey` instead in
   the `initialize` function.

   **Replace**
    ```
    initialize({
      businessId, // Depricated
      clientKey,
      locale
   })
    ```

   **With**
    ```
    initialize({
      clientKey,
      locale
   })
    ```

2. `journeyTemplateId` & `userId` parameters are deprecated and will be removed in a future release. Please rename `journeyTemplateId` -> `journeyDefinitionId` & `userId` -> `referenceNo` instead in the `startJourney` function.
please rename event handlers `onFinished` => `onJourneyFinished`, and we have two more `onJourneyStarted` and `onJourneyCancelled`.

   **Replace**
    ```
    startJourney({
      mount: '#idwise-mount',
      journeyTemplateId: 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', // Depricated
      userId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx", // Depricated
      eventHandlers: {
        onFinished: function(details) { // Depricated
          alert('Thanks for completing the registration')
        }
      }
    })
    ```

   **With**
    ```
    startJourney({
      mount: '#idwise-mount',
      journeyDefinitionId: 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', // Same value as journeyDefId
      referenceNo: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx", // Same value as userId
      eventHandlers: {
        onJourneyStarted: function(details) {
          alert('Journey started, journey id =' + details.journeyId);
        },
        onJourneyFinished: function(details) {
          alert('Journey finished, journey id =' + details.journeyId);
        },
        onJourneyCancelled: function(details) {
          alert('Journey cancelled, journey id =' + details.journeyId);
        },
      }
    })
    ```
