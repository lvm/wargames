# Bandit 9 -> 10

```bash
ssh bandit9@bandit.labs.overthewire.org
```

```
The password for the next level is stored in the file data.txt in one of the few human-readable strings, beginning with several ‘=’ characters.
```

```bash
$ strings ~/data.txt|grep "^="
```

* ssh
* strings
* grep
