---
layout: post
title: "install owncloud in freenas jail"
description: "install owncloud in a freenas jail"
category: administration
tags: [freenas, freebsd, jails, owncloud, nginx, mariadb]
---
{% include JB/setup %}

we will set up owncloud with mariadb and nginx in a freebsd 9 jail (base jail setup described here: <https://nonsenz.github.io/administration/2014/07/17/freenas-jail-bootstrap/>)

## mariadb

install mariadb via portmaster

     > portmaster databases/mariadb55-server
     
edit the rc.conf to enable autostart mariadb while booting

     > vi /etc/rc.conf

add mysql_enable="YES" and saveexit. to start it manually type

    > service mysql-server start
  
initializing the db data dirs with

    > cd /usr/local/   # mysql_install_db assumes its running from here
    > mysql_install_db --user=mysql

setup root user

    > mysqladmin -u root password

## nginx

install via portmaster

    > portmaster www/nginx
    
in the config dialog check webdav support (and maybe more?).
now manually start it via 

    > service nginx start

now you should be able to see the default nginx page when surfing the systems ip.

## php

install it as always with portmaster

    > portmaster lang/php55

after that lets check the config. but first copy the template:

    > cp /usr/local/etc/php.ini-production /usr/local/etc/php.ini

to be save uncomment the session.save_path in the config.
now install some extensions with:

    > portmaster lang/php55-extensions
    
you can find ownclouds requirements and recommendations here: <http://doc.owncloud.org/server/6.0/admin_manual/installation/installation_source.html#prerequisites.>. be sure to enable the mysql extension and disable sqlite as we dont need it.

now edit the php-fpm conf at /usr/local/etc/php-fpm.conf and be set the listen params as follows :

    listen =/var/run/php-fpm.sock
    listen.owner = www
    listen.group = www
    listen.mode = 0660

savexit and make php-fpm autostart by adding php_fpm_enable="YES" to the /etc/rc.conf and start it manually with

    > service php-fpm start
    




## owncloud
