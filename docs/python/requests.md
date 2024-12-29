# Requests

One of my favourite modules

## Always fail on HTTP

```python
import logging
import requests

session = requests.Session()

def response_hook(request, *args, **kwargs):
    try:
        request.raise_for_status()
    except:
        logging.error('Failed %s request to %s', request.request.method, request.request.url)
        logging.error('%s', request.text)
        raise

session.hooks = {'response': response_hook}

try:
    session.get('https://httpstat.us/404')
except requests.exceptions.HTTPError as e:
    if e.response.status_code != 404:
        raise
```
