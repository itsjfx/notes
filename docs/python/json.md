# JSON

## datetime

When dumping JSON from AWS or something with a `datetime`, it'll need to get sanitised a specific way

```python
def json_dumper(x):
    if isinstance(x, datetime.datetime):
        return x.timestamp()
    return json.JSONEncoder().default(x)

print(json.dumps(summary, default=json_dumper))
```
