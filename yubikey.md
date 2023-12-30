# Yubikey

## Stopping the Yubisneeze

You want to remove the default `otp` configured in slot 1 (when you tap the key)

`ykman otp delete 1`

Online people may suggest: `ykman config usb --disable otp` -- this will disable `otp` entirely and it may be useful to keep enabled if you wish to use `otp` in the future.

See: `ykman otp info` to see otp slots