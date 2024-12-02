# Live Update

Calling a page script at a regular interval

# Version
Initial 1.0

# Setup

## Global Script
1. Create a Global Script called "LiveUpdate"
2. Add the input parameters below to the Global Script
   1. Callback
   2. SecondsInterval
3. Drag a *JavaScript* action into the script
4. Add the Javascript below into the JavaScript code property
```javascript
/* Stadium Script v1.0 https://github.com/stadium-software/liveupdate */
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
setInterval(run, interval * 1000);
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
```

## Page
1. Add a script under the page and call it anything you like (e.g. GetData)
2. Add your logic to the script to fetch data and assign it to a control on the page

## Page.Load
1. Drag the "LiveUpdate" script to the page.load event handler
2. Complete the input parameters
   1. Callback: Add the name for the page script to call (e.g. GetData)
   2. SecondsInterval: The interval in seconds at which the script calls the page script

## Working with Stadium Repos
Stadium Repos are not static. They change as additional features are added and bugs are fixed. Using the right method to work with Stadium Repos allows for upgrading them in a controlled manner. How to use and update application repos is described here 

[Working with Stadium Repos](https://github.com/stadium-software/samples-upgrading)