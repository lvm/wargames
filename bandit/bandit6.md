# Bandit 5 -> 6

```bash
ssh bandit5@bandit.labs.overthewire.org
```

```
The password for the next level is stored in a file somewhere under the inhere directory and has all of the following properties: - human-readable - 1033 bytes in size - not executable
```

```bash
$ find ~/inhere/ -type f -size 1033c -exec strings {} \;
```

* ssh
* find
* strings
