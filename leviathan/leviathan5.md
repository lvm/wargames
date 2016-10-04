# Leviathan 4 -> 5

```bash
ssh leviathan4@leviathan.labs.overthewire.org
```

```bash
$ ls -a
.  ..  .bash_logout  .bashrc  .profile  .trash
```

Let's see what's inside `.trash`

```bash
$ cd .trash
$ ls
bin
$ ./bin
```

Well, a string of binary numbers.

Let's fire a Python interpreter:

```python
_bin="01010100 01101001 01110100 01101000 00110100 01100011 01101111 01101011 01100101 01101001 00001010".split(" ")
"".join([chr(int(x,2)) for x in _bin])
```

That's it

* python
