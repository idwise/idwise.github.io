---
layout: default
parent: IDWise Developers
title: Web SDK
has_children: true
nav_order: 4
---

# IDWise Javascript Document Capture SDK
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

This SDK allows you to integrate the IDWise Digital Identity Verification technology in your web app with minimal
effort.
It presents a highly customisable UI that guides the user through a series of steps that prompts them for their ID
documents and/or biometrics depending on how you have configured your journey flow in IDWise backend system. At the end
of this process your app will receive a callback with information about the completed journey which your backend code
can use to securely get the results of this journey. It's that simple!

{% include cta.md %}

## Integration Steps

Please follow these steps to integrate with the document capturing SDK:

1. Add a reference to the style sheet file “https://releases.idwise.com/websdk/latest/idwise.min.css” in the web page
   that will host IDWise Web SDK. For example:

    ```
    <link href="https://releases.idwise.com/websdk/latest/idwise.min.css" rel="stylesheet">
    ```

2. Add a reference to the script file `idwise.min.js` in the web page that will host IDWise Web SDK. For example:

    ```
    <script src="https://releases.idwise.com/websdk/latest/idwise.min.js"></script>
    ```

3. Decide the element that will host the IDWise UI. This can be any block element and can be as simple as this
   example:

    ```
    <div id="idwise-mount"></div>
    ```

4. Start the initialization of IDWise SDK library using `initialize` function. This function takes the following parameters:

* `clientKey` (Mandatory) is string that identify your business and used for authentication. It is provided to you by IDWise team.
* `locale` (Optional) is string of two letter ISO language identifier to determine the language of the UI, the default is `en`. For supporting different languages please contact IDWise team to enable it on your profile. 
* `theme` (Optional) is the theme of UI elements it can be either light, or dark. the values of this parmater can one of the following: 
  * `light`: for light theme.
  * `dark`: for dark theme.
  * `system_default`: for the system defualt theme, which is considered as default behaviour. 

  This call returns a promise to a session instance. The session instance will be used to access the functionality of IDWise system in next steps.

   Example code for the initialization:

    ```
    <script>
        let idwise;
        try {
            idwise = await IDWise.initialize({
              clientKey: 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=',
              locale: 'en',
              theme: 'system_default',
            });
        } catch (error) {
            ...
        }
    </script>
    ```

  

5. Use the IDWise session instance return from the `initialize` function from the previous step to start a new journey. To do so you can call `startJourney` function. The function takes the following parameters:

* `journeyDefinitionId` (Mandatory) is string that identifies the journey flow to be started. This is provided to you by
  IDWise team based on your requirements.
* `mount` (Mandatory) is a string with CSS selector to a single element. This specifies the HTML DOM element where IDWise UI elements should be added. 
* `referenceNo` (Optional but recommended) is a string that used to uniquely identify the user doing the journey in
  your system. This identifier will be attached to this journey and will be provided when fetching the journey data
  and can be used to link the journey back in your system.
* `eventHandlers` (Optional but recommended) An object with list of callbacks to invoke to handle the
  different events fired by IDWise SDK. The following callbacks are supported:
    * `onJourneyStarted`: will be triggered once the user started the journey. it supplies an object that contains `journeyId`.
    * `onJourneyFinished`: will be triggered once the user has gone though the journey and submitted all the steps. it supplies an object that contains `journeyId`. 
    * `onJourneyCancelled`: will be triggered once a user cancels the journey. It supplies an object that contains `journeyId`.
    
  indicate the start and completion of the journey

The following example code shows an overall script covering steps 5 and 6 above:
```javascript

 <script>
   let idwise;   
   IDWise.initialize({
        clientKey: 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=',
        locale: 'en',
      })
      .then((result) => {
        idwise = result;
        idwise.startJourney({
          journeyDefinitionId: 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 
          mount: '#idwise-mount',
          referenceNo: "842098029309823", 
          eventHandlers: {
            onJourneyStarted: function(details) {
              alert('Journey started, journey id =' + details.journeyId)
            },
            onJourneyFinished: function(details) {
              alert('Journey finished, journey id =' + details.journeyId)
            },
            onJourneyCancelled: function(details) {
              alert('Journey cancelled, journey id =' + details.journeyId)
            },
          }
        })
      })
 </script>
```

The `journeyId` identifier can be used to retrieve the data associated with this journey from IDWise Data Fetch API
once you get [Journey Finished](https://developers.idwise.com/webhooks.html#journey-completed-webhook) webhook to your backend.

Note that IDWise SDK will automatically remove the IDWise UI and clean up the used resources when a journey
completes.

That’s it! This is all you need to get core integration up and running and have IDWise Web SDK to enable streamlined
user onboarding journeys.

## Additional Use Cases

IDWise Web SDK can be used in a variety of use cases. Here are some examples:

### Cancelling the Journey

You can cancel a journey by calling `cleanup` function on the IDWise session instance. This function takes no
parameters and returns a promise. The promise will be resolved when the journey is cancelled and the UI elements are
removed.

```javascript
<script>
    var idwise;
    try {
        idwise = await IDWise.initialize({
          clientKey: 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=',
          locale: 'en'
        });
    } catch (error) {
        ...
    }
    
    // Starts the journey
    await idwise.startJourney({ ... });
    // you code here
    // ...
    // Cancel IDWise joruney (for example if the user presses back/cancel button on your UI)' 
    idwise.cleanup();
</script>
```

### Trigger IDWise SDK Again

You can trigger IDWise SDK again by calling `startJourney` function on the IDWise session instance.

```javascript
<script>
    var idwise;
    try {
        idwise = await IDWise.initialize({
          clientKey: 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=',
          locale: 'en'
        });
    } catch (error) {
        ...
    }
    
    // Triggers IDWise SDK Again
    async function restartJourney() {
        if (idwise) {
            await idwise.cleanup();
        }
        await idwise.startJourney({ ... });
    }
    
    // Starts the journey
    await idwise.startJourney({ ... });
    
    // Restart a new joruney.
    restartJourney()
</script>
```

[//]: # (### Resuming an incompleted journey)

[//]: # ()
[//]: # (You can resume an incompleted journey by calling `resumeJourney` function on the IDWise session instance.)

[//]: # ()
[//]: # (```javascript)

[//]: # (coming soon)

[//]: # (```)

