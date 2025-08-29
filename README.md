# Live Update

Calls a page script at a regular interval

# Version
1.1

## Change Log
1.1 Added update end script

# Setup
This module consists of two scripts:
1. "LiveUpdate" to kick off the live update
2. "LiveUpdateEnd" to stop the live update

## Global Script "LiveUpdate"
1. Create a Global Script called "LiveUpdate"
2. Add the input parameters below to the Global Script
   1. Callback
   2. SecondsInterval
3. Drag a *JavaScript* action into the script
4. Add the Javascript below unchanged into the JavaScript code property
```javascript
/* Stadium Script v1.1 https://github.com/stadium-software/liveupdate */
let scope = this;
let callback = ~.Parameters.Input.Callback;
if (!callback) {
    console.error("The 'callback' parameter is required");
    return false;
}
let interval = ~.Parameters.Input.SecondsInterval || 5;
let isRunning = false;
let tries = 0;
let wait = async (milliseconds) => new Promise((resolve) => setTimeout(resolve, milliseconds));
let myInterval = setInterval(run, interval * 1000);
function run() { 
  if (!isRunning) {
    isRunning = true;
    if (callback) {
        tries = 0;
        scriptCaller(callback);
        isRunning = false;
    }
  }
}
async function scriptCaller(script) {
    tries++;
    if (tries > 20) {
    	return false;
    } else {
        try {
            await scope[script]();
            return true;
        } catch (error) {
            wait(100).then(() => scriptCaller(script));
        }
    }
}
window.stopLiveUpdate = function() {
    clearInterval(myInterval);
};
```

## Global Script "LiveUpdateEnd"
1. Create a Global Script called "LiveUpdate"
2. Drag a *JavaScript* action into the script
3. Add the Javascript below unchanged into the JavaScript code property
```javascript
/* Stadium Script v1.1 https://github.com/stadium-software/liveupdate */
window.stopLiveUpdate();
```

## Page
1. Add a script under the page and call it anything you like (e.g. GetData)
2. Add your logic to the script to fetch data and assign it to a control on the page

## Starting the LiveUpdate
1. Drag the "LiveUpdate" script to an event handler
2. Complete the input parameters
   1. Callback: Add the name for the page script to call (e.g. GetData)
   2. SecondsInterval: The interval in seconds at which the script calls the page script

## Ending the LiveUpdate
1. Drag the "LiveUpdateEnd" script to the page.unload event handler

# Working with Stadium Repos
Stadium Repos are not static. They change as additional features are added and bugs are fixed. Using the right method to work with Stadium Repos allows for upgrading them in a controlled manner. How to use and update application repos is described here 

[Working with Stadium Repos](https://github.com/stadium-software/samples-upgrading)