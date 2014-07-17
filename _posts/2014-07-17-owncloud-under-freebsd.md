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

## owncloud
