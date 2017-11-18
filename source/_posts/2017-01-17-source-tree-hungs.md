---
layout: post
title: "SourceTree 操作被挂起"
category: "git"
tags: ["git","SourceTree"]
---


#### 出现的问题

```shell
The server's host key is not cached in the registry. You
have no guarantee that the server is the computer you
think it is.
The server's rsa2 key fingerprint is:
ssh-rsa 2048 59:ca:22:af:f9:8e:6f:f7:87:b3:ea:da:1b:2b:aa:f2
If you trust this host, enter "y" to add the key to
PuTTY's cache and carry on connecting.
If you want to carry on connecting just once, without
adding the key to the cache, enter "n".
If you do not trust this host, press Return to abandon the
connection.
```

#### 解决

手动连接一次 git 服务器，比如 GitLab，[StackOverflow](http://stackoverflow.com/questions/32437659/cannot-pull-git-remote-repository-from-sourcetree)

```shell
"C:\Program Files (x86)\Atlassian\SourceTree\tools\putty\plink.exe" gitlab.com
```

