# Bandit 13 -> 14

```bash
ssh bandit13@bandit.labs.overthewire.org
```

```
The password for the next level is stored in /etc/bandit_pass/bandit14 and can only be read by user bandit14. For this level, you don’t get the next password, but you get a private SSH key that can be used to log into the next level. Note: localhost is a hostname that refers to the machine you are working on
```

```bash
$ ssh -i sshkey.private bandit14@localhost cat /etc/bandit_pass/bandit14
```

* ssh
* cat
