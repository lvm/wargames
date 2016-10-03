# Bandit 10 -> 11

```bash
ssh bandit10@bandit.labs.overthewire.org
```

```
The password for the next level is stored in the file data.txt, which contains base64 encoded data
```

```bash
$ base64 -d ~/data.txt
```

* ssh
* base64
