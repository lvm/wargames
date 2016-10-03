# Bandit 21 -> 22

```bash
ssh bandit21@bandit.labs.overthewire.org
```

```
A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.
```

```bash
$ ls /etc/cron.d/cronjob*
$ cat ${BANDIT22_CRONJOB}
$ cat ${BANDIT22_CRONJOB_SCRIPT}
```

```
Note:
${BANDIT22_CRONJOB} and ${BANDIT22_CRONJOB_SCRIPT} are the cronjob/script of user bandit22, I wrote it this way to avoid spoilers.
```

* ssh
* cron
* cat
