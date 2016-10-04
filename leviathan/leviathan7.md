# Leviathan 6 -> 7

```bash
ssh leviathan6@leviathan.labs.overthewire.org
```

```bash
$ ls
leviathan6
$ ./leviathan6
usage: ./leviathan6 <4 digit code>
```

We can do two things:

* Bruteforce
* `ltrace`

Let's try the latter

```bash
$ ltrace  ./leviathan6 1234
```

It seems that it uses `atoi` to convert the input to `int`, now we need to find where it does the comparison

```bash
$ gdb ./leviathan6
...
   0x08048555 <+72>:	cmp    0x1c(%esp),%eax
...
```

Here it is ! but more importantly, let's see where `0x1c` it's called too

```
...
   0x08048516 <+9>:	movl   $0x1bd3,0x1c(%esp)
...
```

Fantastic, it moves `0x1bd3` to `0x1c` (excuse my poor *asm*).  
Once again, let's go to Python:

```python
>>> print int("0x1bd3",0)
```

```bash
$ ./leviathan6 ${HEX_PIN}
```

That's it.


* ln
* ltrace
* http://www.hep.wisc.edu/~pinghc/x86AssmTutorial.htm
