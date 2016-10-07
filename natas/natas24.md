# Natas 23 -> 24

```
http://natas23.natas.labs.overthewire.org
```

```
Username: natas23
```

A password field and the source code:

```php
<?php
    if(array_key_exists("passwd",$_REQUEST)){
        if(strstr($_REQUEST["passwd"],"iloveyou") && ($_REQUEST["passwd"] > 10 )){
            echo "<br>The credentials for the next level are:<br>";
            echo "<pre>Username: natas24 Password: <censored></pre>";
        }
        else{
            echo "<br>Wrong!<br>";
        }
    }
    // morla / 10111
?>
```

It will search for the key `passwd` in `$_REQUEST` and the string `iloveyou` AND that is bigger than 10. wat.php.  
So the most simple thing to do is...

```bash
$ curl --user natas23:${NATAS23_PASSWD} "http://natas23.natas.labs.overthewire.org/?passwd=11iloveyou"
```

* php
* http://php.net/manual/en/language.types.string.php#language.types.string.conversion
