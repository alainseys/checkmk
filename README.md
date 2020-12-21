# checkmk

# Tested on
<ul>
  <li>Debian9</li>
  <li>Centos7</li>
</ul>

very easy just run ./configure --with-nagios4 if you are running nagios3 or below run ./configure --with-nagios
### post install steps:

edit /usr/local/nagios/etc/nagios.cfg
and add : broker_module=/usr/local/lib/mk-livestatus/livestatus.o /usr/local/nagios/var/rw/live
restart nagios process

### Broker check (if it is running)
cat /usr/local/nagios/var/nagios.log

you should see something with times hour, minute etc...

download nagvis from : http://www.nagvis.org/
extract tarbal
./install follow the steps and add 

### Add Code to 000-default.conf

```  
vim: syntax=apache ts=4 sw=4 sts=4 sr noet
Alias /nagvis "/usr/local/nagvis/share"
<Directory "/usr/local/nagvis/share">
  Options FollowSymLinks
  AllowOverride None
  <IfModule mod_authz_core.c>
    # Apache >= 2.4
    Require all granted
  </IfModule>
  <IfModule !mod_authz_core.c>
    # Apache < 2.4
    Order allow,deny
    Allow from all
  </IfModule>

  #AuthName "NagVis Access"
  #AuthType Basic
  #AuthUserFile /usr/local/nagios/etc/htpasswd.users
  #Require valid-user 
  <IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteBase /nagvis    
    #RewriteCond %{REQUEST_URI} ^/nagvis(/config\.php|/index\.php|/|)(\?.*|)$
    #RewriteRule ^(.*)$ /nagvis/frontend/nagvis-js/%1%2 [R=301,L]    
    RewriteCond %{REQUEST_URI} ^/nagvis/frontend/(wui|nagvis-js)
    RewriteCond %{QUERY_STRING} map=(.*)
    RewriteRule ^(.*)$ /nagvis/frontend/nagvis-js/index.php?mod=Map&act=view&show=%1 [R=301,L]    
    RewriteCond %{REQUEST_URI} ^/nagvis/frontend(/wui)?/?(index.php)?$
    RewriteRule ^(.*)$ /nagvis/frontend/nagvis-js/index.php [R=301,L]   
RewriteCond %{REQUEST_URI} ^/nagvis/frontend/nagvis-js
    RewriteCond %{QUERY_STRING} !mod
    RewriteCond %{QUERY_STRING} rotation=(.*)
    RewriteRule ^(.*)$ /nagvis/frontend/nagvis-js/index.php?mod=Rotation&act=view&show=%1 [R=301,L]
  </IfModule>
</Directory>
</code>
```
### Restart apache & nagios
systemctl restart nagios
systemctl restart apache2
now navigate to http://127.0.0.1/nagvis login with default credentials admin / admin

#### Debugging
if you see a white page you have an version of php installed that is not supported by nagvis i recomend php 5.7 or php 7
