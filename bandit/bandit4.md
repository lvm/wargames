# Bandit 3 -> 4

```bash
ssh bandit3@bandit.labs.overthewire.org
```

```
The password for the next level is stored in a hidden file in the inhere directory.
```

```bash
$ find ~/inhere/ -type f -exec cat {} \;
```

Alt solution

```bash
$ ls -a ~/inhere
$ cat ~/inhere/.hidden
```


* ssh
* find
* ls
* cat
