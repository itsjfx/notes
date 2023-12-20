# FIDO2 Azure AD / Entra ID

## FIDO2 Linux Firefox Azure AD / Entra ID Fix

[FIDO2 is enabled by default and supported in Firefox as of 114.0](https://www.mozilla.org/en-US/firefox/114.0/releasenotes/)

However the Azure login website (at this time, 17 Aug 2023) still does not support FIDO2 on Firefox on Linux as it glitches out.
![[../assets/fido2-azure-ad-failure.png]]

> We couldn't verify you or the key you used. If you are using a security key, make sure this is your key and try again.

If you use Tampermonkey or Greasemonkey and this gist I wrote -- you can override some JS variables on the Azure login to allow it to work.

[https://gist.github.com/itsjfx/e9e63130ba17a180a2e42294a2d955d5/](https://gist.github.com/itsjfx/e9e63130ba17a180a2e42294a2d955d5/)  

[Raw link for Tampermonkey or Greasemonkey](https://gist.github.com/itsjfx/e9e63130ba17a180a2e42294a2d955d5/raw/75157271fae2e7f89b13e8ec43e2037ac673c187/azure_login_fido2_fix.user.js)

Azure's website (on the server side) will stop FIDO2 from working for Firefox as it'll detect your user agent.

Another workaround is to set your user agent to Chrome on Linux (e.g. `general.useragent.override` or with an addon) and it'll work as expected

## Failure

TODO write a blog post on _how_ I dug into this

Microsoft's FIDO2 code throws `The operation failed for an unknown transient reason` after doing a `window.navigator.credentials.get()` (thanks [justinsteven](https://github.com/webcompat/web-bugs/issues/101753#issuecomment-1728897194) for the summary)