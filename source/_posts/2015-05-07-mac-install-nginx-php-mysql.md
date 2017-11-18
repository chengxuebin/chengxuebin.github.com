---
layout: post
title: "Mac OS 安装 nginx, php, mysql"
category: "mac"
tags: ["mac", 'nginx', 'php', 'mysql']
---



## 常用位置：
1. nginx: 
    - 安装位置：`/usr/local/Cellar/nginx`
    - log: `/usr/local/var/log/nginx/error.log`
    - 配置位置：`/usr/local/etc/nginx`
    - document root: `/var/www`
    
2. php
    - 安装位置：`/usr/local/Cellar/php56/5.6.8/`
    - 配置位置：`/usr/local/etc/php/5.6/php.ini`
    
3. mysql
    - 查看server信息：`mysqld --help --verbose`
    - 安装位置：`/usr/local/Cellar/mysql` 
 
 
## 安装

引用一篇非常详细的博客：[Install Nginx, PHP-FPM, MySQL and phpMyAdmin on OS X Mavericks or Yosemite](http://blog.frd.mn/install-nginx-php-fpm-mysql-and-phpmyadmin-on-os-x-mavericks-using-homebrew/)

### Xcode

```
xcode-select --install
```

### Homebrew

```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Check for any conflicts or problems (If you have confilcts, sort them out before you continue with this guide):

```
brew doctor
```

Update and upgrade its formulas in case you already had Homebrew installed before:

```
brew update && brew upgrade
```

### PHP-FPM

```
brew tap homebrew/dupes
brew tap homebrew/php
```

Now install it with the following arguments:

```
brew install --without-apache --with-fpm --with-mysql php56
```

Setup PHP CLI binary

If you want to use the PHP command line binary, you need to update the $PATH environment variable of your shell profile:

```
# If you use Bash    
echo 'export PATH="/usr/local/sbin:$PATH"' >> ~/.bash_profile
. ~/.bash_profile

# If you use ZSH
echo 'export PATH="/usr/local/sbin:$PATH"' >> ~/.zshrc
. ~/.zshrc
```

Setup auto start

Create a folder for our LaunchAgents and symlink the start/stop service:

```
mkdir -p ~/Library/LaunchAgents
ln -sfv /usr/local/opt/php56/homebrew.mxcl.php56.plist ~/Library/LaunchAgents/
```

And start PHP-FPM:

```
launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.php56.plist
```

Make sure PHP-FPM is listening on port 9000:

```
lsof -Pni4 | grep LISTEN | grep php
```

The output should look something like this:

```
php-fpm   69659  frdmn    6u  IPv4 0x8d8ebe505a1ae01      0t0  TCP 127.0.0.1:9000 (LISTEN)
php-fpm   69660  frdmn    0u  IPv4 0x8d8ebe505a1ae01      0t0  TCP 127.0.0.1:9000 (LISTEN)
php-fpm   69661  frdmn    0u  IPv4 0x8d8ebe505a1ae01      0t0  TCP 127.0.0.1:9000 (LISTEN)
php-fpm   69662  frdmn    0u  IPv4 0x8d8ebe505a1ae01      0t0  TCP 127.0.0.1:9000 (LISTEN)    
```

### MySQL

Next step is to install MySQL:

```
brew install mysql
```

Setup auto start

```
ln -sfv /usr/local/opt/mysql/*.plist ~/Library/LaunchAgents
```

And start the database server:

```
launchctl load ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist
```

```
mysql_secure_installation
```

Test connection

```
mysql -uroot -p
```

### phpMyAdmin

Install autoconf, which is needed for the installation of phpMyAdmin:

```
brew install autoconf
```

Set $PHP_AUTOCONF:

```
# If you use Bash
echo 'PHP_AUTOCONF="'$(which autoconf)'"' >> ~/.bash_profile && . ~/.bash_profile
# If you use ZSH
echo 'PHP_AUTOCONF="'$(which autoconf)'"' >> ~/.zshrc && . ~/.zshrc
```

Let's start with the installation of phpMyAdmin:

```
brew install phpmyadmin
```


### Nginx

Install the default Nginx with:

```
brew install nginx
```

Setup auto start

Since we want to use port 80 have to start the Nginx process as root:

```
sudo cp -v /usr/local/opt/nginx/*.plist /Library/LaunchDaemons/
sudo chown root:wheel /Library/LaunchDaemons/homebrew.mxcl.nginx.plist
```

Test web server

Start Nginx for the first with:

```
sudo launchctl load /Library/LaunchDaemons/homebrew.mxcl.nginx.plist
```

Stop Nginx again:

```
sudo launchctl unload /Library/LaunchDaemons/homebrew.mxcl.nginx.plist
```

Make alias:

```
alias nginx.start='sudo launchctl load /Library/LaunchDaemons/homebrew.mxcl.nginx.plist'
alias nginx.stop='sudo launchctl unload /Library/LaunchDaemons/homebrew.mxcl.nginx.plist'
alias nginx.restart='nginx.stop && nginx.start'
alias php-fpm.start="launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.php56.plist"
alias php-fpm.stop="launchctl unload -w ~/Library/LaunchAgents/homebrew.mxcl.php56.plist"
alias php-fpm.restart='php-fpm.stop && php-fpm.start'
alias mysql.start="launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist"
alias mysql.stop="launchctl unload -w ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist"
alias mysql.restart='mysql.stop && mysql.start'
alias nginx.logs.error='tail -250f /usr/local/etc/nginx/logs/error.log'
alias nginx.logs.access='tail -250f /usr/local/etc/nginx/logs/access.log'
alias nginx.logs.default.access='tail -250f /usr/local/etc/nginx/logs/default.access.log'
alias nginx.logs.default-ssl.access='tail -250f /usr/local/etc/nginx/logs/default-ssl.access.log'
alias nginx.logs.phpmyadmin.error='tail -250f /usr/local/etc/nginx/logs/phpmyadmin.error.log'
alias nginx.logs.phpmyadmin.access='tail -250f /usr/local/etc/nginx/logs/phpmyadmin.access.log'
```

