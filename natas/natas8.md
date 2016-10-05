# Natas 7 -> 8

```
http://natas7.natas.labs.overthewire.org
```

```
Username: natas7
```

This time we have two sections **Home** and **About**, each with a link to `?page=${section}`.   
Perhaps it tries to `include` the file `${section}.php`, What happens if we go to `?page=index`? Mmh, it fails.  
Let's see the source code:  

```
<!-- hint: password for webuser natas8 is in /etc/natas_webpass/natas8 -->
```

Well, that was easy.


* php
* include
