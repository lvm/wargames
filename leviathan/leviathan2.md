# Leviathan 1 -> 2

```bash
ssh leviathan1@leviathan.labs.overthewire.org
```

```bash
$ ls -a
$ ./check
password: qwe
Wrong password, Good Bye ...
```

Let's try again with `ltrace`

```bash
$ ltrace ./check
```

Now that we found that there's a call to `strcmp`, let's use that.

```bash
$ ./check
password:
$ id
uid=12001(leviathan1) gid=12001(leviathan1) euid=12002(leviathan2) groups=12002(leviathan2),12001(leviathan1)
```

Now we need to read `/etc/leviathan_pass/leviathan2`

* ltrace
