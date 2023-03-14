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

      4. Start the initialization of IDWise SDK library using `initialize` function. This function takes three
         parameters
         which identify your business and are shared with you during your registration process with IDWise.

         Example code for the initialization:
    
          ```
          <script>
              let idwise;
              try {
                  idwise = await IDWise.initialize({
                    clientKey: 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=',
                    locale: 'en',
                  });
              } catch (error) {
                  ...
              }
          </script>
          ```

          This call returns a promise to a session instance. The session instance will be used to access the functionality of IDWise system in next steps.

5. Use the IDWise session instance to start a new journey. To do so you can call `startJourney` function. The function
   takes three parameters:

* `journeyDefinitionId` (Mandatory) Identifies the steps of the journey to be started. This is provided to you by
  IDWise team based on your requirements and what documents or biometrics you want to ask your users for
* `referenceNo` (Optional but recommended) An identifier that uniquely identifies the user carrying out this journey in
  your system. This identifier will be attached to this journey and will be provided when fetching the journey data
  and can be used to link the journey back in your system
* `mount` (Mandatory) The HTML DOM element where IDWise UI elements should be added. This was specified in Step 4
  above
* `eventHandlers` (Optional but recommended) An object with list of callbacks to invoke to handle the
  different events fired by IDWise SDK. There are currently two events supported: onStarted and onFinished. Which
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
          mount: '#idwise-mount',
          journeyDefinitionId: 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', // journey definition id
          referenceNo: "842098029309823", // An identifier that uniquely idenfies the user carrying out this journey
          eventHandlers: {
            onFinished: function(details) {
              alert('Thanks for completing the registration')
            }
          }
        })
      })
 </script>
```

You can access the identifier of the journey that was completed by accessing the parameter provided on the `onFinished`
event callback. This identifier can be used to retrieve the data associated with this journey from IDWise Data Fetch API
once you get the [webhook call](https://idwi.se/webhooks) to your backend.

Note that IDWise SDK will automatically remove the UI elements it created and clean up the used resources when a journey
completes and after triggering the `onFinished` event.

And that’s it! This is all you need to get core integration up and running and have IDWise Web SDK to enable streamlined
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
    
    // Cancels the Journey
    async function cancelJourney(){
        if (idwise) {
            idwise.cleanup();
        }
        else {
            alert("No active journey");
        }
    }
    
    // Starts the journey
    await idwise.startJourney({ ... });
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
    
    // Cancels the Journey
    async function cancelJourney(){
        if (idwise) {
            await idwise.cleanup();
        }
    }
    
    // Triggers IDWise SDK Again
    async function resetJourney() {
        cancelJourney();
        await idwise.startJourney({ ... });
    }
    
    // Starts the journey
    await idwise.startJourney({ ... });
</script>
```

[//]: # (### Resuming an incompleted journey)

[//]: # ()
[//]: # (You can resume an incompleted journey by calling `resumeJourney` function on the IDWise session instance.)

[//]: # ()
[//]: # (```javascript)

[//]: # (coming soon)

[//]: # (```)

