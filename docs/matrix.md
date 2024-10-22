# Matrix

## Generating registration tokens

Ref: <https://matrix-org.github.io/synapse/latest/usage/administration/admin_api/registration_tokens.html>#

Grab token from browser or Element desktop via Dev Tools (Ctrl + Shift + I)
```
➜  ~  curl -X POST 'https://matrix.jfx.ac/_synapse/admin/v1/registration_tokens/new' -H 'Authorization: Bearer TOKEN' --data-raw '{"uses_allowed":10, "length": 6
4}'
{"token":"x","uses_allowed":10,"pending":0,"completed":0,"expiry_time":null}
```
