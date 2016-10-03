# Bandit 6 -> 7

```bash
ssh bandit6@bandit.labs.overthewire.org
```

```
The password for the next level is stored somewhere on the server and has all of the following properties: - owned by user bandit7 - owned by group bandit6 - 33 bytes in size
```

```bash
$ find / -user bandit7 -group bandit6 -type f -size 33c -exec strings {} \;
```

* ssh
* find
* strings
