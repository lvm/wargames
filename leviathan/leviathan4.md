# Leviathan 3 -> 4

```bash
ssh leviathan3@leviathan.labs.overthewire.org
```

```bash
$ ls
level3
```

This time let's try directly with `ltrace`

```bash
$ ltrace ./level3
```

Good, again a call to `strcmp`, let's see what happens.

```bash
$ ./level3
...
[You've got shell]!
```

Just simple read the `leviathan4` password file

* ltrace
