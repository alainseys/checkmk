# checkmk

very easy just run ./configure --with-nagios4 if you are running nagios3 or below run ./configure --with-nagios

runs on debian9 and centos7

post install steps:

edit /usr/local/nagios/etc/nagios.cfg
and add : broker_module=/usr/local/lib/mk-livestatus/livestatus.o /usr/local/nagios/var/rw/live
restart nagios process

and check if the broker is reporting
cat /usr/local/nagios/var/nagios.log

you should see somthing with times hour, minute etc...



