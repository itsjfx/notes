# Threads

## ThreadPoolExcecutor

Simple example using threads to do something

```python
#!/usr/bin/env python3

import sys
from concurrent.futures import ThreadPoolExecutor, as_completed
import time
import uuid
import random

def something(payload):
    print(f'Completing task with payload {payload}...', file=sys.stderr)
    time.sleep(random.uniform(1, 5))
    data = str(uuid.uuid4())
    print(f'Got data {data} for payload {payload}', file=sys.stderr)
    return str(uuid.uuid4())

def do_stuff(n):
    with ThreadPoolExecutor(max_workers=None) as executor:
        results = [executor.submit(something, i) for i in range(1, n)]
        for future in as_completed(results):
            yield future.result()

for stuff in do_stuff(11):
    print(stuff)
```
