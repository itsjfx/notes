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

## Context ThreadPool

```python
class ScopedThreadPool(ThreadPoolExecutor):
    """
    Wrapper around ThreadPoolExecutor that ensures all futures are completed before exiting the context
    """
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.futures = []

    def submit(self, *args, **kwargs):
        future = super().submit(*args, **kwargs)
        self.futures.append(future)
        return future

    def __exit__(self, *args, **kwargs):
        while True:
            done, pending = wait(self.futures)
            # may have futures that creates more futures
            if len(done) == len(self.futures):
                break

        result = super().__exit__(*args, **kwargs)
        if exceptions := [f.exception() for f in self.futures if f.exception()]:
            if len(exceptions) == 1:
                raise exceptions[0]
            raise BaseExceptionGroup('failed', exceptions)
        return result

with ScopedThreadPool() as executor:
    executor.submit(func)

with ScopedThreadPool() as executor:
    for region in {'ap-southeast-2', 'us-east-1'}:
        @executor.submit
        def job(region=region):
            print(region)
```
