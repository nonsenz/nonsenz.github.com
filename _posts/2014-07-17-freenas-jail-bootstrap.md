---
layout: post
title: "create nice freenas base jail"
description: "default first steps to create a nice freebsd jail with freenas 9.2.1"
category: administration
tags: [freenas, freebsd, jails]
---
{% include JB/setup %}

## whats the problem?

since freenas 9.2.1 does not support native jail cloning (looks like 9.3 will: <https://bugs.freenas.org/issues/5074>) and it is not recommended to do it via warden in the background: <http://forums.freenas.org/index.php?threads/what-is-the-recommended-way-to-clone-a-jail.21156/#post-122791>, one has to manually setup the jail with the standard tools before doing anything else. here are the m default things to do:

## setting it up

### create the jail

like always create a new jail (portjail), edit settings if you like, name it and press ok.

### enabling ssh

because we want to work in the jail over ssh, we have to enable it. click on the created jail in the webinterface and click the shell symbol on the bottom to open a shell in the browser. 

~~~ bash
> vi /etv/rc.conf
~~~

change sshd_enable="NO" to "YES" and savexit.

~~~ bash
> service sshd start
~~~

now you can login via ssh to the jails ip. 

### adding your user

because you want your personal user:

~~~ bash
> adduser
~~~

you can add the new user to the wheel group to give him root privileges. you'll be asked if you wan the new user to be invited to other groups. simply type in "wheel".

### enabling ports

the best way to install tools on your machine is via ports with some helpertools. because freenas 9 uses freebsd 9 we have to modify the ports config to be able to use the newer package format. besides this you add other things to your /etc/make.conf to optimize your build process:

~~~ bash
> vi /etc/make.conf
~~~

here is a small example:

~~~
#Use clang instead of gcc, only needed for versions before 10.0
CC=clang
CXX=clang++
CPP=clang-cpp
#Compile everything in a ram disk and use ccache, see our tutorial
WRKDIRPREFIX=/ram
WITH_CCACHE_BUILD=yes
#The newer pkgng format, only needed for versions before 10.0
WITH_PKGNG=yes
~~~



### tools
zsh, vim, ...
