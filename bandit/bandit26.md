# Bandit 25 -> 26

```bash
ssh bandit25@bandit.labs.overthewire.org
```

```
Logging in to bandit26 from bandit25 should be fairly easy… The shell for user bandit26 is not /bin/bash, but something else. Find out what it is, how it works and how to break out of it.
```

```bash
$ ssh -i bandit26.sshkey bandit26@localhost
$ grep bandit26 /etc/passwd
$ cat ${BANDIT26_SHELL}
```

We'll see a banner that says `bandit26` and then we're kicked. This one got me thinking for a while, tried obscure SSH configurations and nothing worked until I re-read the shell being used by bandit26.  
Here lies the trick:

* It uses `more`.
* `more` will wait for us to keep scrolling when the content is larger than the available space to display it.
* `more` uses `vi`. I'm an `emacs` user so this was kinda tricky (rtfm: `man vi`)
* Remember the banner? Count how many lines it has.
* Resize the window.
* Try again.

That's it. Of course I recommend going through all the obscure SSH configurations/documentation because it'll be useful in other scenarios, but to be honest, the solution is way easier.

```
Note:
${BANDIT26_SHELL} is the shell of user bandit26, I wrote it this way to avoid spoilers.
```

* ssh
* more
* vi
