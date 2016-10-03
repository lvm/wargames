# Bandit 22 -> 23

```bash
ssh bandit22@bandit.labs.overthewire.org
```

```
A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

NOTE: Looking at shell scripts written by other people is a very useful skill. The script for this level is intentionally made easy to read. If you are having problems understanding what it does, try executing it to see the debug information it prints.
```

```bash
$ ls /etc/cron.d/cronjob*
$ cat ${BANDIT23_CRONJOB}
$ cat ${BANDIT23_CRONJOB_SCRIPT}
```

Since this script uses the `whoami` command to findout who is running it, we need to modify it.
Protip: If we append `-x` to the hashbang the script will work in *DEBUG* mode.

```bash
$ cd `mktemp -d`
$ cp ${BANDIT23_CRONJOB_SCRIPT} ./script_b23.sh
$ nano ./script_b23.sh
$ ./script_b23.sh
```


```
Note:
${BANDIT23_CRONJOB} and ${BANDIT23_CRONJOB_SCRIPT} are the cronjob/script of user bandit23, I wrote it this way to avoid spoilers.
```


* ssh
* cp
* cat
* cron
* nano
* mktemp
