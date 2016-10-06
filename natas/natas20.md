# Natas 19 -> 20

```
http://natas19.natas.labs.overthewire.org
```

```
Username: natas19
```

Same form, no source code and this message:


```
This page uses mostly the same code as the previous level, but session IDs are no longer sequential... 
```

Well, let's see what the `PHPSESSID` has to say.


```bash
$ curl -v --user natas19:${NATAS19_PASSWD} "http://natas19.natas.labs.overthewire.org/index.php?username=a&password=a"
...
PHPSESSID=3531362d61
PHPSESSID=3336372d61
PHPSESSID=3435392d61
PHPSESSID=3534372d61
PHPSESSID=3431362d61
...
```

Do you see the pattern?  
What about our target? `natas19`?

```bash
$ curl -v --user natas19:${NATAS19_PASSWD} "http://natas19.natas.labs.overthewire.org/index.php?username=natas19&password=a"
...
PHPSESSID=3435332d6e617461733139
...
```

it changed.  
Anyway, back to the first lot of `PHPSESSID`.  

```
353136[2d61]
333637[2d61]
343539[2d61]
353437[2d61]
343136[2d61]
```

This remain the same all along, the rest of the numbers (int?) did change. Also, what remain the same all along was our request `username=[a]`. Let's try something:  

```python
>>> "2d61".decode('hex')
'-a'
```

hah !  
What about our second request, the one with `natas19`. Actually, now reading it again, there's a `2d` in there.  
I'll discard everything before `2d`...

```python
>>> "2d6e617461733139".decode('hex')
'-natas19'
```

Great. But what are those numbers?

```python
>>> map(lambda n: n.decode('hex'), ['353136', '333637', '343539', '353437', '343136'])
['516', '367', '459', '547', '416']
```

Also hex encoded integers. Same as the previous level.  
I'll asume that this time `PHPSESSID` is `{}{}.format('123'.encode('hex'), '-username'.encode('hex'))`


* php
* http://php.net/manual/en/reserved.variables.session.php
* curl
* lynx
* seq


