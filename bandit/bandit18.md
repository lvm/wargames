# Bandit 17 -> 18

Since we got a SSH private key in the previous level, we should save that key somewhere with the correct permission

```bash
$ chmod 600 bandit17.key
```

```bash
ssh -i bandit17.key bandit17@bandit.labs.overthewire.org
```

```
There are 2 files in the homedirectory: passwords.old and passwords.new. The password for the next level is in passwords.new and is the only line that has been changed between passwords.old and passwords.new

NOTE: if you have solved this level and see ‘Byebye!’ when trying to log into bandit18, this is related to the next level, bandit19
```

```bash
$ diff ~/passwords.old ~/passwords.new
```

* chmod
* ssh
* diff
