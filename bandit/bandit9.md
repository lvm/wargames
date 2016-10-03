# Bandit 8 -> 9

```bash
ssh bandit8@bandit.labs.overthewire.org
```

```
The password for the next level is stored in the file data.txt and is the only line of text that occurs only once
```

```bash
$ sort -rn ~/data.txt|uniq -c
```

* ssh
* sort
* uniq
