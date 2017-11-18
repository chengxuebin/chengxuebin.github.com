---
layout: post
title: "MySQL升级到5.7"
category: "mysql"
tags: ["linux","cenos"]
---



Step 1: Uninstall MySQL Packages
First uninstall all the MySQL packages installed on your server

```shell

> yum remove mysql mysql-server

> yum list installed | grep mysql

> yum remove mysql-client mysql-server mysql-common mysql-devel

```


```shell

> rm -rf /var/lib/mysql/

> rm -rf /etc/my.cnf

```

Step 2: Romove MySQL Directory
Now we need to remove MySQL data directory from system which by default exists at /var/lib/mysql. If you didn’t find this, It may be changed to some other place, which you can find in my.cnf file with variable datadir. Delete the /var/lib/mysql directory from system but we prefer to rename it to keep a backup of existing files.

```shell
> mv /var/lib/mysql /var/lib/mysql_old_backup
```

Step 3: Install MySQL Packages Again
After removing MySQL completely, install it again using yum package manager, It will re create mysql directory under /var/lib/.

```shell
> yum install mysql mysql-server
```

```shell
> mysql_upgrade -uroot -p

> grep 'temporary password' /var/log/mysqld.log

> mysql_secure_installation

> we can set it in my.cnf file
> [mysqld]
> validate_password_policy=LOW
```

### 参考
>[How to Install Latest MySQL 5.7.9 on RHEL/CentOS 7/6/5 and Fedora](http://www.tecmint.com/install-latest-mysql-on-rhel-centos-and-fedora/)



