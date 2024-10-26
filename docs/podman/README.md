# Podman

## File permissions

* `PODMAN_USERNS=keep-id`
    * files owned by the current user / rootless
    * unset = files will be owned by root / root container

## GUI

Get a GUI working quickly for Debian/Ubuntu

```bash
apt-get update && apt-get install -y xvfb x11vnc i3 xterm
export DISPLAY=:99
Xvfb :99 -screen 0 1024x768x16 &
x11vnc -display :99 -forever &
i3
```
