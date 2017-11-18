---
layout: post
title: "Supervisor 使用小结"
category: "linux"
tags: ["linux","server","mac"]
---



### 安装

通过 pip 来安装

```shell
> pip install supervisor
```

如果还没有 pip，运行下面的命令：

```shell
> curl -O http://python-distribute.org/distribute_setup.py
> python distribute_setup.py
> sudo easy_install pip
> sudo pip install supervisor
```

在 Mac OS 下，还可能通过 homebrew 安装：

```shell
> brew install distribute
> sudo easy_install pip
> sudo pip install supervisor
``` 

### 创建配置文件

一旦安装完成，运行 `echo_supervisord_conf` 将打印一个标准的配置模板，可将其保存到下列配置路径中：

```shell
/usr/local/share/etc/supervisord.conf
/usr/local/share/supervisord.conf
./supervisord.conf
./etc/supervisord.conf
/etc/supervisord.conf
```

将标准配置输出到某个路径下的命令是：

```shell
> echo_supervisord_conf > /etc/supervisord.conf
```

### 运行 supervisor

supervisor 提供了2个运行程序：守护程序 `supervisord` 和客户端程序 `supervisorctl`。守护程序在后台运行，管理其它进程；客户端程序用于操作supervisor。

启动守护进程：

```shell
> supervisord 
> supervisord -c /path/to/supervisord.conf    # 或指定配置文件
```

#### Mac OS 配置 `launchd`

添加文件 `/Library/LaunchDaemons/com.agendaless.supervisord.plist`

```xml
<?xml version=\"1.0\" encoding=\"UTF-8\"?>
<!DOCTYPE plist PUBLIC \"-//Apple//DTD PLIST 1.0//EN\" \"http://www.apple.com/DTDs/PropertyList-1.0.dtd\">
<plist version=\"1.0\">
    <dict>
        <key>KeepAlive</key>
        <dict>
            <key>SuccessfulExit</key>
            <false/>
        </dict>
        <key>Label</key>
        <string>com.agendaless.supervisord</string>
        <key>ProgramArguments</key>
        <array>
            <string>/usr/local/share/python/supervisord</string>
            <string>-n</string>
            <string>-c</string>
            <string>/usr/local/share/supervisor/supervisord.conf</string>
        </array>
        <key>RunAtLoad</key>
        <true/>
    </dict>
</plist>
```

注册 plist 
```shell
> launchctl load /Library/LaunchDaemons/com.agendaless.supervisord.plist
```

### 使用 supervisorctl

```shell
> supervisorctl -c /etc/supervisord.conf

> status    # 查看程序状态
> stop foo   # 关闭 foo 程序
> start foo  # 启动 foo 程序
> restart foo    # 重启 foo 程序
> reread    ＃ 读取有更新（增加）的配置文件，不会启动新添加的程序
> update    ＃ 重启配置文件修改过的程序
> tail foo stdout
> tail -f foo
> tail -f foo stderr
```

通过命令行直接运行：

```shell
> sudo supervisorctl start foo
```


### 修改配置文件

```conf
[unix_http_server]
file=/tmp/supervisor.sock   ; UNIX socket 文件，supervisorctl 会使用
;chmod=0700                 ; socket 文件的 mode，默认是 0700
;chown=nobody:nogroup       ; socket 文件的 owner，格式： uid:gid

[inet_http_server]         ; HTTP 服务器，提供 web 管理界面
port=127.0.0.1:9001        ; Web 管理后台运行的 IP 和端口，如果开放到公网，需要注意安全性
;username=user              ; 登录管理后台的用户名
;password=123               ; 登录管理后台的密码

[supervisord]
logfile=/tmp/supervisord.log ; 日志文件，默认是 $CWD/supervisord.log
logfile_maxbytes=50MB        ; 日志文件大小，超出会 rotate，默认 50MB
logfile_backups=10           ; 日志文件保留备份数量默认 10
loglevel=info                ; 日志级别，默认 info，其它: debug,warn,trace
pidfile=/tmp/supervisord.pid ; pid 文件
nodaemon=false               ; 是否在前台启动，默认是 false，即以 daemon 的方式启动
minfds=1024                  ; 可以打开的文件描述符的最小值，默认 1024
minprocs=200                 ; 可以打开的进程数的最小值，默认 200

; the below section must remain in the config file for RPC
; (supervisorctl/web interface) to work, additional interfaces may be
; added by defining them in separate rpcinterface: sections
[rpcinterface:supervisor]
supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface

[supervisorctl]
serverurl=unix:///tmp/supervisor.sock ; 通过 UNIX socket 连接 supervisord，路径与 unix_http_server 部分的 file 一致
;serverurl=http://127.0.0.1:9001 ; 通过 HTTP 的方式连接 supervisord

; 包含其他的配置文件
[include]
files = relative/directory/*.ini    ; 可以是 *.conf 或 *.ini
```

当更新配置文件后，重新启动守护进程：

```shell
supervisord -c /etc/supervisord.conf
```


### 常用程序

#### nginx 
```conf
[program:nginx]
command=sudo /usr/local/sbin/nginx
autostart=false
autorestart=true
```

#### laravel queue 
```conf
[program:laravel-worker]
process_name=%(program_name)s_%(process_num)02d
command=php /home/forge/app.com/artisan queue:work sqs --sleep=3 --tries=3 --daemon
autostart=true
autorestart=true
user=forge
numprocs=8
redirect_stderr=true
stdout_logfile=/home/forge/app.com/worker.log
```

### 可能遇到的问题：

#### 问题：
```shell
* Starting Supervisor daemon manager...
Error: Another program is already listening on a port that one of our HTTP servers is configured to use.  Shut this program down first before starting supervisord.
For help, use /usr/bin/supervisord -h
   ...fail!
```   

解决办法：
删除 supervisor.sock

