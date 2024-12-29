# Logging

## Enable global logger

```python
import logging

logging.basicConfig(level='INFO')
```

## Simple TAB based logger

```python
import logging

logging.basicConfig(level='INFO')
logging.basicConfig(level='INFO', format='%(levelname)s\t%(message)s')
```

## Accepting log level from CLI args

```python
if __name__ == '__main__':
    import argparse
    parser = argparse.ArgumentParser()
    parser.add_argument('-l', '--log', choices=('debug', 'info', 'warning', 'error', 'critical'), default='info', help="Logging level (default: %(default)s)")
    args = parser.parse_args()
    logging.basicConfig(level=getattr(logging, args.log.upper()))
```
