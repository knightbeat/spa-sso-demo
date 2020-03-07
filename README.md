
```bash
sudo vim /etc/apache2/extra/httpd-vhosts.conf
```

```xml
<VirtualHost *:80>
    ServerAdmin admin@example.com
    ServerName myhttpd.com
    ServerAlias www.myhttpd.com
    DocumentRoot /Library/WebServer/Documents
    ErrorLog /private/var/log/apache2/apache2local_error.log
    CustomLog /private/var/log/apache2/acache2local_access.log combined
</VirtualHost>

<VirtualHost *:80>
    ServerAdmin admin@example.com
    ServerName filecab.com
    ServerAlias www.filecab.com
    DocumentRoot /Library/WebServer/Documents/filecab.com/build
    <Directory "/Library/WebServer/Documents/filecab.com/build">
        RewriteEngine on
        # Don't rewrite files or directories
        RewriteCond %{REQUEST_FILENAME} -f [OR]
        RewriteCond %{REQUEST_FILENAME} -d
        RewriteRule ^ - [L]
        # Rewrite everything else to index.html to allow html5 state links
        RewriteRule ^ index.html [L]
    </Directory>
    ErrorLog /private/var/log/apache2/filecab_error.log
    CustomLog /private/var/log/apache2/filecab_access.log combined
</VirtualHost>
```
Reference: To react-router work on Apache web server https://gkedge.gitbooks.io/react-router-in-the-real/content/apache.html

Copy the ReactApp's `build` directory to `/Library/WebServer/Documents/filecab.com/`

```bash
sudo vim /etc/apache2/httpd.conf
```
```bash

LoadModule rewrite_module libexec/apache2/mod_rewrite.so

<Directory "/Library/WebServer/Documents">
    #
    # Possible values for the Options directive are "None", "All",
    # or any combination of:
    #   Indexes Includes FollowSymLinks SymLinksifOwnerMatch ExecCGI MultiViews
    #
    # Note that "MultiViews" must be named *explicitly* --- "Options All"
    # doesn't give it to you.
    #
    # The Options directive is both complicated and important.  Please see
    # http://httpd.apache.org/docs/2.4/mod/core.html#options
    # for more information.
    #
    Options Indexes FollowSymLinks

    #
    # AllowOverride controls what directives may be placed in .htaccess files.
    # It can be "All", "None", or any combination of the keywords:
    #   AllowOverride FileInfo AuthConfig Limit
    #
    AllowOverride All

    #
    # Controls who can get stuff from this server.
    #
    Require all granted
</Directory>
```

```bash
sudo vim /etc/hosts
```

```erlang
127.0.0.1       filecab.com
127.0.0.1       myhttpd.com
```

```bash
sudo apachectl restart
```

Go to http://filecab.com/

