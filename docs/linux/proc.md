# proc

`man proc`

## Open Files

Helpful for troubleshooting what files are opened by a process, you can open file descriptors in `ls /proc/PID/fd`. It also tells you where stdin/stdout/stderr are going.

I generally use `strace` instead but this can be useful too.

## Environment Variables

You can see environment variables for processes via `cat /proc/PID/environ`.

The output is null-terminated, so `cat /proc/PID/environ | tr '\0' '\n'` will give you more meaningful output.

This can be used to search all processes for specific variables, etc.

## Executable

You can find the executable path for a process via `ls -la /proc/PID/exe`.
