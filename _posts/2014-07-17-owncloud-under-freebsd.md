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

now you should be able to see the default nginx page when surfing the systems ip. next thing to do is move the original config and create a new one:

    > cd /usr/local/etc/nginx
    > mv nginx.conf nginx.conf.orig
    
insert the following code to the new config:

    user  www www;
    worker_processes 1;
    worker_priority 15;
     
    pid /var/run/nginx.pid;
    error_log  /var/log/nginx-error.log  info;
     
    events {
      worker_connections  512;
      accept_mutex on;
      use kqueue;
    }
     
    http {
      include       conf.d/options;
      include       mime.types;
      default_type  application/octet-stream;
      access_log  /var/log/nginx-access.log main buffer=32k;
     
      include sites/*.site;
    }

next we have to create the options file /usr/local/etc/nginx/conf.d/options and paste:

    client_body_timeout 5s;
    client_header_timeout 5s;
    keepalive_timeout 75s;
    send_timeout 15s;
    charset utf-8;
    gzip off;
    gzip_static on;
    gzip_proxied any;
    ignore_invalid_headers on;
    keepalive_requests 50;
    keepalive_disable none;
    max_ranges 1;
    msie_padding off;
    open_file_cache max=1000 inactive=2h;
    open_file_cache_errors on;
    open_file_cache_min_uses 1;
    open_file_cache_valid 1h;
    output_buffers 1 512;
    postpone_output 1440;
    read_ahead 512K;
    recursive_error_pages on;
    reset_timedout_connection on;
    sendfile on;
    server_tokens off;
    server_name_in_redirect off;
    source_charset utf-8;
    tcp_nodelay on;
    tcp_nopush off;
    gzip_disable "MSIE [1-6]\.(?!.*SV1)";
    limit_req_zone $binary_remote_addr zone=gulag:1m rate=60r/m;
    log_format main '$remote_addr $host $remote_user [$time_local] "$request" $status $body_bytes_sent "$http_referer" "$http_user_agent" $ssl_cipher $request_time';

finally create the sites dir 

    > mkdir /usr/local/etc/nginx/sites
    
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
