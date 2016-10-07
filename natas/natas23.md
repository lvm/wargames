# Natas 22 -> 23

```
http://natas22.natas.labs.overthewire.org
```

```
Username: natas22
```

An empty page with the source code

```php
<? 
session_start(); 

if(array_key_exists("revelio", $_GET)) { 
    // only admins can reveal the password 
    if(!($_SESSION and array_key_exists("admin", $_SESSION) and $_SESSION["admin"] == 1)) { 
    header("Location: /"); 
    } 
} 
?> 


<html> 
<head> 
<!-- This stuff in the header has nothing to do with the level --> 
<link rel="stylesheet" type="text/css" href="http://natas.labs.overthewire.org/css/level.css"> 
<link rel="stylesheet" href="http://natas.labs.overthewire.org/css/jquery-ui.css" /> 
<link rel="stylesheet" href="http://natas.labs.overthewire.org/css/wechall.css" /> 
<script src="http://natas.labs.overthewire.org/js/jquery-1.9.1.js"></script> 
<script src="http://natas.labs.overthewire.org/js/jquery-ui.js"></script> 
<script src=http://natas.labs.overthewire.org/js/wechall-data.js></script><script src="http://natas.labs.overthewire.org/js/wechall.js"></script> 
<script>var wechallinfo = { "level": "natas22", "pass": "<censored>" };</script></head> 
<body> 
<h1>natas22</h1> 
<div id="content"> 

<? 
    if(array_key_exists("revelio", $_GET)) { 
    print "You are an admin. The credentials for the next level are:<br>"; 
    print "<pre>Username: natas23\n"; 
    print "Password: <censored></pre>"; 
    }
?>
```

If we pass `$_GET['revelio']` and we're not logged it'll redirect us to `/`. Otherwise, it'll show the credentials.
How do we do to avoid being redirected? We use `curl` :-)

```bash
$ curl --user natas22:${NATAS22_PASSWD} http://natas22.natas.labs.overthewire.org/?revelio=1
```

* php
