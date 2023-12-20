# JavaScript

## Inject JavaScript into a browser

This snippet is not as useful if the site blocks `script-src` in their CSP, and Tampermonkey and Greasemonkey are much better: 
```js
const code = `
    console.log('hello');
`;

const script = document.createElement('script');
script.textContent = code;
(document.documentHead || document.documentElement).appendChild(script);
script.remove();
```

## Monkey patching

Simple monkey patching, prints `args` and `results` to `stdout`.

Using the script above or a user script, you can monkey patch easily in the browser and in Node.

e.g. to monkey patch an [`async` browser method](https://developer.mozilla.org/en-US/docs/Web/API/CredentialsContainer/get):

```js
const _get = navigator.credentials.get;

navigator.credentials.get = async function(...args) {
    // modify the arguments how you want. arguments is also set
    // on a browser, you probably want to JSON.stringify() this so it stays in history, otherwise you will lose the value
    // however logging the actual value is helpful to see object prototypes etc
    console.log('args', args, JSON.stringify(args));

    let res = await _get.apply(this, args);

    // modify the return value
    console.log('res', res, JSON.stringify(res));
    return res;
}
```