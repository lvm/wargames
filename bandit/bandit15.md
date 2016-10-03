# Bandit 14 -> 15

```bash
ssh bandit14@bandit.labs.overthewire.org
```

```
The password for the next level can be retrieved by submitting the password of the current level to port 30000 on localhost.
```

```bash
$ echo ${BANDIT14_PASSWD}|nc localhost 30000
```

* ssh
* echo
* nc

