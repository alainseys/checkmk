# checkmk

#Tested on
<ul>
  <li>Debian9</li>
  <li>Centos7</li>
</ul>

very easy just run ./configure --with-nagios4 if you are running nagios3 or below run ./configure --with-nagios

runs on debian9 and centos7

post install steps:

edit /usr/local/nagios/etc/nagios.cfg
and add : broker_module=/usr/local/lib/mk-livestatus/livestatus.o /usr/local/nagios/var/rw/live
restart nagios process

and check if the broker is reporting
cat /usr/local/nagios/var/nagios.log

you should see somthing with times hour, minute etc...

download nagvis from : http://www.nagvis.org/
extract tarbal
./install follow the steps and add 

<code>
  
# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
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

  # To enable Nagios basic auth on NagVis use the following options
  # Just uncomment it. Maybe you need to adjust the path to the
  # Auth user file.
  #
  # If you use the NagVis internal auth mechanism based on the web
  # for you won't need this.
  #
  #AuthName "NagVis Access"
  #AuthType Basic
  #AuthUserFile /usr/local/nagios/etc/htpasswd.users
  #Require valid-user

  # With installed and enabled mod_rewrite there are several redirections
  # available to fix deprecated and/or wrong urls. None of those rules is
  # mandatory to get NagVis working.
  <IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteBase /nagvis

    # Use mod_rewrite for old url redirection even if there are php files which
    # redirect the queries itselfs. In some cases the mod_rewrite redirect
    # is better than the php redirect.
    #
    # Using the php redirect seems to be better in some cases where https/http servers
    # are mixed. For example in OMD setups where using apache own mode and https in the
    # frontend and http in the backend apache servers.
    #
    # Disabling this redirect by default in the hope that the php direct works better.
    #RewriteCond %{REQUEST_URI} ^/nagvis(/config\.php|/index\.php|/|)(\?.*|)$
    #RewriteRule ^(.*)$ /nagvis/frontend/nagvis-js/%1%2 [R=301,L]

    # Redirect old regular map links
    RewriteCond %{REQUEST_URI} ^/nagvis/frontend/(wui|nagvis-js)
    RewriteCond %{QUERY_STRING} map=(.*)
    RewriteRule ^(.*)$ /nagvis/frontend/nagvis-js/index.php?mod=Map&act=view&show=%1 [R=301,L]

    # Without map= param
    RewriteCond %{REQUEST_URI} ^/nagvis/frontend(/wui)?/?(index.php)?$
    RewriteRule ^(.*)$ /nagvis/frontend/nagvis-js/index.php [R=301,L]

    # Redirect old rotation calls
RewriteCond %{REQUEST_URI} ^/nagvis/frontend/nagvis-js
    RewriteCond %{QUERY_STRING} !mod
    RewriteCond %{QUERY_STRING} rotation=(.*)
    RewriteRule ^(.*)$ /nagvis/frontend/nagvis-js/index.php?mod=Rotation&act=view&show=%1 [R=301,L]
  </IfModule>
</Directory>

</code>


