# Bandit 19 -> 20

```bash
ssh bandit19@bandit.labs.overthewire.org
```

```
To gain access to the next level, you should use the setuid binary in the homedirectory. Execute it without arguments to find out how to use it. The password for this level can be found in the usual place (/etc/bandit_pass), after you have used to setuid binary.
```

```bash
$ ./bandit20-do cat /etc/bandit_pass/bandit20
```

* ssh
* cat
* su 
