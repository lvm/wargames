# Bandit 20 -> 21

```bash
ssh bandit20@bandit.labs.overthewire.org
```

```
There is a setuid binary in the homedirectory that does the following: it makes a connection to localhost on the port you specify as a commandline argument. It then reads a line of text from the connection and compares it to the password in the previous level (bandit20). If the password is correct, it will transmit the password for the next level (bandit21).

NOTE: To beat this level, you need to login twice: once to run the setuid command, and once to start a network daemon to which the setuid will connect.

NOTE 2: Try connecting to your own network daemon to see if it works as you think
```

Connect again to `bandit20`

```bash
$ nc -lp 12345
```

Back on the first connection

```bash
$ ./suconnect 12345
```

Then paste the password on the 2nd connection and wait for `suconnect` to give our next password.


* ssh
* nc
