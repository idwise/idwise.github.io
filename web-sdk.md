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

The IDWise JavaScript Document Capture SDK provides a convenient way to integrate the IDWise Digital Identity Verification technology into your web application. With minimal effort, you can incorporate a highly customisable user interface (UI) that guides users through a series of steps, prompting them for their ID documents and/or biometrics based on your configured journey flow in the IDWise backend system. Upon completion of the process, your app receives a callback with information about the finished journey, allowing your backend code to securely retrieve the journey results. It's that simple!

{% include cta.md %}

## Integration Steps

Follow these steps to integrate the document capturing SDK:

1. Add a reference to the style sheet file `https://releases.idwise.com/websdk/latest/idwise.min.css` in the web page that will host the IDWise Web SDK. For example:

    ```
    <link href="https://releases.idwise.com/websdk/latest/idwise.min.css" rel="stylesheet">
    ```

2. Add a reference to the script file `idwise.min.js` in the web page that will host the IDWise Web SDK. For example:

    ```
    <script src="https://releases.idwise.com/websdk/latest/idwise.min.js"></script>
    ```

3. Choose the element that will host the IDWise UI. This can be any block element and can be as simple as the following example:

    ```
    <div id="idwise-mount"></div>
    ```

4. Initialize the IDWise SDK library using the `initialize` function. This function requires the following parameters:

* `clientKey` (Mandatory): A string that identifies your business and is used for authentication. Provided by the IDWise team.
* `locale` (Optional): A two-letter ISO language identifier (e.g., "en") to determine the language of the UI. The default is "en". Contact the IDWise team to enable support for different languages.
* `theme` (Optional): The theme of UI elements, which can be either `light` or `dark`. Alternatively, use `system_default` for the system default theme.

  The `initialize` function returns a promise that resolves to a session instance. This session instance is used to access the functionality of the IDWise system in the subsequent steps.

  Example code for initialization:

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


5. Use the IDWise session instance obtained from the `initialize` function to start a new journey by calling the `startJourney` function. This function requires the following parameters:

* `journeyDefinitionId` (Mandatory): A string that identifies the journey flow to be started. Provided by the IDWise team based on your requirements.
* `mount` (Mandatory): A string with a CSS selector representing a single element. This specifies the HTML DOM element where IDWise UI elements should be added.
* `referenceNo` (Optional but recommended): A string used to uniquely identify the user undergoing the journey in your system. This identifier will be attached to the journey and can be used to link the journey back to your system when fetching the journey data.
* `eventHandlers` (Optional but recommended): An object containing callbacks to handle different events fired by the IDWise SDK. The supported callbacks include:
  * `onJourneyStarted`: Triggered when the user starts the journey. Provides an object containing the `journeyId`.
  * `onJourneyFinished`: Triggered when the user completes all the steps of the journey. Provides an object containing the `journeyId`.
  * `onJourneyCancelled`: Triggered when the user cancels the journey. Provides an object containing the `journeyId`.

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
          alert('Journey started, journey id =' + details.journeyId);
        },
        onJourneyFinished: function(details) {
          alert('Journey finished, journey id =' + details.journeyId);
        },
        onJourneyCancelled: function(details) {
          alert('Journey cancelled, journey id =' + details.journeyId);
        },
      }
    });
  });
</script>
```

The `journeyId` identifier can be used to retrieve the data associated with the journey from the IDWise Data Fetch API once you receive the [Journey Finished](https://developers.idwise.com/webhooks.html#journey-completed-webhook) webhook in your backend.

Note that the IDWise SDK automatically removes the IDWise UI and cleans up the used resources when a journey completes.

That's it! You have successfully completed the core integration and enabled streamlined user onboarding journeys using the IDWise Web SDK. You can find [here](https://github.com/idwise/idwise-web-sdk-samples/blob/main/web-sdk-v4-sample-app.html) a sample HTML file that demonstrates the integration steps described above.



## Additional Use Cases

The IDWise Web SDK supports various use cases. Here are some examples:

### Cancelling the Journey
To cancel a journey, call the `cleanup` function on the IDWise session instance. This function takes no parameters and returns a promise. The promise is resolved when the journey is cancelled and the UI elements are removed.

```javascript
<script>
  var idwise;
  try {
    idwise = await IDWise.initialize({
      clientKey: 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=',
      locale: 'en'
    });
  } catch (error) {
    // Handle initialization error
  }
  
  // Starts the journey
  await idwise.startJourney({ ... });
  // Your code here
  // ...
  // Cancel the IDWise journey (e.g., if the user presses the back/cancel button on your UI)
  await idwise.cleanup();
</script>
```

### Triggering IDWise SDK Again
To trigger the IDWise SDK again, call the `startJourney` function on the IDWise session instance.

```javascript
<script>
  var idwise;
  try {
    idwise = await IDWise.initialize({
      clientKey: 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=',
      locale: 'en'
	});
  } catch (error) {
    // Handle initialization error
  }
  
  // Triggers the IDWise SDK again
  async function restartJourney() {
    if (idwise) {
      await idwise.cleanup();
    }
    await idwise.startJourney({ ... });
  }
  
  // Starts the journey
  await idwise.startJourney({ ... });
  
  // Restart a new journey
  restartJourney();
</script>
```


[//]: # (### Resuming an incompleted journey)

[//]: # ()
[//]: # (You can resume an incompleted journey by calling `resumeJourney` function on the IDWise session instance.)

[//]: # ()
[//]: # (```javascript)

[//]: # (coming soon)

[//]: # (```)

