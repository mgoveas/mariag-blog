---
title: Throttle and debounce
date: "2020-03-01T22:12:03.284Z"
description: "Improve JavaScript performance with throttle and debounce"
---

Throttling and debouncing are useful tools to tackle the rate at which a function is called. Throttling is limiting the number of times a function is called over a period of time. Debouncing ensures that a fixed amount of time has passed before the function is called. 

### Why should we use this? 
I recently worked on an save and resume feature for a JS form. The user enters data in 20-30 fields and if they choose to abandon the form, they are currently presented with a popup that asks them to save the draft data. The next step would be to auto-save the data as the user enters it. 
With the save button, if the user clicks several times, we throttle the number of hits the API would get. For the auto-save feature, we can debouce the save until the user has interacted with the form for a set period of time. 

Both these functions are available out of the box in [underscore.js](http://underscorejs.org) 

##### debounce simple implementation
```
const debounce = (func, delay) => {
  let timeout;
  return function() {
    clearTimeout(timeout);
    timeout = setTimeout(() => func.apply(this, arguments), delay);
  }
}
  ```
##### throttle simple implementation

```
const throttle = (func, limit) => {
  let timeout;
  let tsLastRun;
  let tsNow = Date.now();
  return function() {
    if (!tsLastRun) {
      func.apply(this, arguments);
      tsLastRun = tsNow;
    } else {
      clearTimeout(timeout);
      timeout = setTimeout(function() {
        if ((tsNow - tsLastRun) >= limit) {
          func.apply(this, arguments);
          tsLastRun = tsNow;
        }
      }, limit - tsNow - tsLastRun);
    }
  }
}
```

This is a nice way of letting JavaScript interact with user actions while providing a good user experience. Useful implementations include resizing the browser, auto complete and infinite scrolling. 
