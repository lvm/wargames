# Bandit 12 -> 13

```bash
ssh bandit12@bandit.labs.overthewire.org
```

```
The password for the next level is stored in the file data.txt, which is a hexdump of a file that has been repeatedly compressed. For this level it may be useful to create a directory under /tmp in which you can work using mkdir. For example: mkdir /tmp/myname123. Then copy the datafile using cp, and rename it using mv (read the manpages!)
```

```bash
$ cd `mktemp -d`
$ cp ~/data.txt .
$ xxd -r data.txt|file -
```

Here we discover it's a gzip'd file.

```bash
$ xxd -r data.txt|zcat|file -
```

Also a bzip'd file.

```bash
$ xxd -r data.txt|zcat|bzcat|file -
```

And gzip'd file.

```bash
$ xxd -r data.txt|zcat|bzcat|zcat|file -
```

And tar'd file.

```bash
$ xxd -r data.txt|zcat|bzcat|zcat|tar xvf -
```

So we get a file called `data5.bin`

```bash
$ file data5.bin
$ tar xvf data5.bin
```

Now a file called `data6.bin`

```bash
$ file data6.bin
$ bzcat data6.bin|file -
$ bzcat data6.bin|tar xvf -
```

And now a file called `data8.bin`

```bash
$ zcat data8.bin
```


* ssh
* mktemp
* xxd
* file
* zcat
* gzip
* bzcat
* bzip2
* tar

