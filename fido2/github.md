# FIDO2 GitHub

<https://github.com/settings/security> -> PassKeys
## "Passkey registration failed" on Firefox

Using a new FIDO key?

If will receive this error cause [Firefox doesn't support setting up the PIN for a hardware key](https://www.yubico.com/blog/firefox-support-for-fido2-authenticators-is-here/). You can set it up using `ykman` like so:

`ykman fido access change-pin`

and try again


More discussion here: <https://github.com/orgs/community/discussions/67791>

## Multiple accounts on a single FIDO2 device

You can have multiple accounts on a single FIDO2 device!

![[fido2-github-multiple-accounts.png]]

The GitHub sends a FIDO2 challenge with an empty `allowCredentials` argument
```json
{
    "publicKey": {
        "challenge": x,
        "timeout": 60000,
        "rpId": "github.com",
        "allowCredentials": [],
        "userVerification": "required"
    }
}
```

See [the webauthn2 spec for more info](https://www.w3.org/TR/webauthn-2/#client-side):
> The [Relying Party](https://www.w3.org/TR/webauthn-2/#relying-party) invokes [navigator.credentials.get()](https://w3c.github.io/webappsec-credential-management/#dom-credentialscontainer-get) with an empty [allowCredentials](https://www.w3.org/TR/webauthn-2/#dom-publickeycredentialrequestoptions-allowcredentials) argument. This means that the [Relying Party](https://www.w3.org/TR/webauthn-2/#relying-party) does not necessarily need to first identify the user.
> As a consequence, a [discoverable credential capable](https://www.w3.org/TR/webauthn-2/#discoverable-credential-capable) [authenticator](https://www.w3.org/TR/webauthn-2/#authenticator) can generate an [assertion signature](https://www.w3.org/TR/webauthn-2/#assertion-signature) for a [discoverable credential](https://www.w3.org/TR/webauthn-2/#discoverable-credential) given only an [RP ID](https://www.w3.org/TR/webauthn-2/#rp-id), which in turn necessitates that the [public key credential source](https://www.w3.org/TR/webauthn-2/#public-key-credential-source) is stored in the [authenticator](https://www.w3.org/TR/webauthn-2/#authenticator) or [client platform](https://www.w3.org/TR/webauthn-2/#client-platform).