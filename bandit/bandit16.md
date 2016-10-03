# Bandit 15 -> 16

```bash
ssh bandit15@bandit.labs.overthewire.org
```

```
The password for the next level can be retrieved by submitting the password of the current level to port 30001 on localhost using SSL encryption.

Helpful note: Getting “HEARTBEATING” and “Read R BLOCK”? Use -ign_eof and read the “CONNECTED COMMANDS” section in the manpage. Next to ‘R’ and ‘Q’, the ‘B’ command also works in this version of that command…
```

```bash
$ echo ${BANDIT15_PASSWD}|openssl s_client -ign_eof -host localhost -port 30001
```

* ssh
* echo
* openssl
