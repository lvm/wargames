# Bandit 18 -> 19

```bash
ssh bandit18@bandit.labs.overthewire.org
```

```
The password for the next level is stored in a file readme in the homedirectory. Unfortunately, someone has modified .bashrc to log you out when you log in with SSH.
```

```bash
$ ssh bandit18@bandit.labs.overthewire.org "ls -a"
$ ssh bandit18@bandit.labs.overthewire.org "cat ~/readme"
```

* ssh
* cat
