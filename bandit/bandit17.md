# Bandit 16 -> 17

```bash
ssh bandit16@bandit.labs.overthewire.org
```

```
The credentials for the next level can be retrieved by submitting the password of the current level to a port on localhost in the range 31000 to 32000. First find out which of these ports have a server listening on them. Then find out which of those speak SSL and which don’t. There is only 1 server that will give the next credentials, the others will simply send back to you whatever you send to it.
```

```bash
$ nmap localhost -sV -p 31000-32000
```

After we get the list of open ports and found which one is correct:

```bash
$ echo ${BANDIT16_PASSWD}|openssl s_client -ign_eof -host localhost -port ${SSL_PORT}
```

* ssh
* nmap
* echo
* openssl
