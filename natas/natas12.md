# Natas 11 -> 12

```
http://natas11.natas.labs.overthewire.org
```

```
Username: natas11
```

This time we have a form to set the background color with this legend:  

```
 Cookies are protected with XOR encryption
```

And the source code:

```php
$defaultdata = array( "showpassword"=>"no", "bgcolor"=>"#ffffff");

function xor_encrypt($in) {
    $key = '<censored>';
    $text = $in;
    $outText = '';

    // Iterate through each character
    for($i=0;$i<strlen($text);$i++) {
    $outText .= $text[$i] ^ $key[$i % strlen($key)];
    }

    return $outText;
}

function loadData($def) {
    global $_COOKIE;
    $mydata = $def;
    if(array_key_exists("data", $_COOKIE)) {
    $tempdata = json_decode(xor_encrypt(base64_decode($_COOKIE["data"])), true);
    if(is_array($tempdata) && array_key_exists("showpassword", $tempdata) && array_key_exists("bgcolor", $tempdata)) {
        if (preg_match('/^#(?:[a-f\d]{6})$/i', $tempdata['bgcolor'])) {
        $mydata['showpassword'] = $tempdata['showpassword'];
        $mydata['bgcolor'] = $tempdata['bgcolor'];
        }
    }
    }
    return $mydata;
}

function saveData($d) {
    setcookie("data", base64_encode(xor_encrypt(json_encode($d))));
}

$data = loadData($defaultdata);

if(array_key_exists("bgcolor",$_REQUEST)) {
    if (preg_match('/^#(?:[a-f\d]{6})$/i', $_REQUEST['bgcolor'])) {
        $data['bgcolor'] = $_REQUEST['bgcolor'];
    }
}

saveData($data);

...

if($data["showpassword"] == "yes") {
    print "The password for natas12 is <censored><br>";
}

```

Let's start bottom-up:  

* If `$data["showpassword"] == "yes"`, we get the password.  
* `$data` comes from `loadData()`  
* `loadData()` uses `$_COOKIE`

Let's see what else we can obtain with `curl`

```bash
$ curl --user natas11:${NATAS11_PASSWD} --head http://natas11.natas.labs.overthewire.org
...
Set-Cookie: data=ClVLIh4ASCsCBE8lAxMacFMZV2hdVVotEhhUJQNVAmhSEV4sFxFeaAw
...
```

Good !  
  
We already have:  

* the `$_COOKIE`
* the code of `loadData()`
* the code of `saveData()`
* the code of `xor_encrypt`

The thing about XOR encryption is that we use a key to encode a message and to decode it we need to use the same key on the encrypted message to go back to the original message. So...

```php
$defaultdata = array("showpassword"=>"no", "bgcolor"=>"#ffffff");

$cookie = "ClVLIh4ASCsCBE8lAxMacFMZV2hdVVotEhhUJQNVAmhSEV4sFxFeaAw";
$fakedata = array("showpassword"=>"yes", "bgcolor"=>"#ffffff");

function xor_encrypt($in) {
  global $defaultdata;
  $key = json_encode($defaultdata); //'<censored>';
  $text = $in;
  $outText = '';

  // Iterate through each character
  for($i=0;$i<strlen($text);$i++) {
    $outText .= $text[$i] ^ $key[$i % strlen($key)];
  }

  return $outText;
}

echo xor_encrypt(base64_decode($cookie));
```

should give us the original `$key`.

```php
$defaultdata = array("showpassword"=>"no", "bgcolor"=>"#ffffff");

$cookie = "ClVLIh4ASCsCBE8lAxMacFMZV2hdVVotEhhUJQNVAmhSEV4sFxFeaAw";
$fakedata = array("showpassword"=>"yes", "bgcolor"=>"#ffffff");

function xor_encrypt($in) {
  $key = "${original_key}"; // this is not the original key, that's left to you to discover :-)
  $text = $in;
  $outText = '';

  // Iterate through each character
  for($i=0;$i<strlen($text);$i++) {
    $outText .= $text[$i] ^ $key[$i % strlen($key)];
  }

  return $outText;
}

print base64_encode(xor_encrypt(json_encode($fakedata)));
```

Great, we have forged the content of the `$_COOKIE`.  
Let's use curl to complete this:

```bash
curl --user natas11:${NATAS11_PASSWD} --cookie "data=${COOKIE_CONTENT}" http://natas11.natas.labs.overthewire.org
```

That's it.

* php
* base64_encode
* json_decode
* array_key_exists
* preg_match
* global
* $_COOKIE
* curl
* https://en.wikipedia.org/wiki/XOR_cipher
