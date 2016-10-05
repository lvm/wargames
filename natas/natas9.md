# Natas 8 -> 9

```
http://natas8.natas.labs.overthewire.org
```

```
Username: natas8
```

Again we're asked for a secret input and we get the source too.  
It simply compares our `$_POST['secret']` with `$encodedSecret`.

```php
$encodedSecret = "3d3d516343746d4d6d6c315669563362";

function encodeSecret($secret) {
    return bin2hex(strrev(base64_encode($secret)));
}
```

Well, `encodeSecret` takes an argument and passes through `bin2hex`, then through `strrev` and finally it's encoded in `base64`. We just need to do the reverse:  

```php
function decodeSecret($secret) {
    return base64_decode(strrev(hex2bin($secret)));
}
```

Keep movin' !


* php
* base64_encode
* base64_decode
* strrev
* bin2hex
* hex2bin
