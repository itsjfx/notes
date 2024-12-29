# defaultdict

Instead of doing 

```python
a = {}
key = 'b'
if not key in a:
    a[key] = []
a[key].append(1)
```

You can do
```python
from collections import defaultdict
a = defaultdict(list)
key = 'b'
a[key].append(1)
```
Works with `set` or `int`, etc
