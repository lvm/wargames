# Bandit 23 -> 24

```bash
ssh bandit23@bandit.labs.overthewire.org
```

```
A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

NOTE: This level requires you to create your own first shell-script. This is a very big step and you should be proud of yourself when you beat this level!

NOTE 2: Keep in mind that your shell script is removed once executed, so you may want to keep a copy around…
```

```bash
$ ls /etc/cron.d/cronjob*
$ cat ${BANDIT24_CRONJOB}
$ cat ${BANDIT24_CRONJOB_SCRIPT}
```

Since this script uses the `whoami` command to find out who is running it, we need to modify it.
Protip: If we append `-x` to the hashbang the script will work in *DEBUG* mode.

```bash
$ cd `mktemp -d`
$ cp ${BANDIT24_CRONJOB_SCRIPT} ./script_b24.sh
$ nano ./script_b24.sh
$ ./script_b24.sh
```

We'll find that the script executes all the scripts from a *directory* and then they're removed.
After thinking about a way to read `/etc/bandit_pass/bandit24` (which is where the password is stored) and saving it's content to somewhere we could read, I came up with the very same solution of a couple of levels before.

```bash
$ ssh bandit23@bandit.labs.overthewire.org
$ nc -lp 12345
```

And back to the first connection, where we create a script that only needs to read the *bandit24's password file* and pass it to our netcat server. Then we wait.

```bash
$ cp our_script.sh ${BANDIT24_DIRECTORY}
```

Remember that the script needs to have *execution permissions* ! (ie: `chmod +x ...`)



```
Note:
${BANDIT24_CRONJOB}, ${BANDIT24_CRONJOB_SCRIPT} and ${BANDIT24_DIRECTORY} are the cronjob/script of user bandit24, I wrote it this way to avoid spoilers.
```


* ssh
* cp
* cat
* cron
* nano
* chmod
* netcat
* mktemp
