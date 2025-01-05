# Tmux

## Ctrl Shift Tab hotkey broken

If your Ctrl + Shift + Tab hotkey is broken and bound as `C-S-Tab`, replace it with `C-BTab`

e.g. `bind-key -n C-BTab previous-window`

See: <https://github.com/tmux/tmux/issues/4136#issuecomment-2394260822>

Broke in `tmux 3.5` around Sept of 2024

## Debugging

```bash
cd /tmp
tmux kill-server
tmux -vv new
```

Will write verbose logs. For troubleshoot hotkeys, `tmux-server.log` is a helpful.
