# sed

## Use another character instead of `/` for regex

https://www.gnu.org/software/sed/manual/sed.html#The-_0022s_0022-Command

```
The / characters may be uniformly replaced by any other single character within any given s command. The / character (or whatever other character is used in its stead) can appear in the regexp or replacement only if it is preceded by a \ character.
```

e.g. `echo blargh | sed 's#blargh#hello#'` is valid