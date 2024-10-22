# Timeouts

## Unref

[`timeout.unref()`](https://nodejs.org/api/timers.html#timeoutunref)

> When called, the active `Timeout` object will not require the Node.js event loop to remain active. If there is no other activity keeping the event loop running, the process may exit before the `Timeout` object's callback is invoked. Calling `timeout.unref()` multiple times will have no effect
