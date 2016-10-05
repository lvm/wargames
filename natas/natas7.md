# Natas 6 -> 7

```
http://natas6.natas.labs.overthewire.org
```

```
Username: natas6
```

We're asked for a secret input and thankfully we get the source code too !

```php
<?

include "includes/secret.inc";

    if(array_key_exists("submit", $_POST)) {
        if($secret == $_POST['secret']) {
        print "Access granted. The password for natas7 is <censored>";
    } else {
        print "Wrong secret";
    }
    }
?>
```

What's inside that `include`?

* php
* include
