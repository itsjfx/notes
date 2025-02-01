# systemd

## Check if a service is not installed

```bash
if ! sudo systemctl cat xxx.service &>/dev/null; then
    ...
fi

sudo systemctl enable --now xxx.service
```
