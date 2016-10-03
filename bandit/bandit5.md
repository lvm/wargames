# Bandit 4 -> 5

```bash
ssh bandit4@bandit.labs.overthewire.org
```

```
The password for the next level is stored in the only human-readable file in the inhere directory. Tip: if your terminal is messed up, try the “reset” command.
```

```bash
$ find ~/inhere/ -type f -exec strings {} \;
```

* ssh
* find
* strings
