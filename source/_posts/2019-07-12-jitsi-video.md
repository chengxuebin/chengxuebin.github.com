---
layout: post
title: "Jitsi Meet 视频技术"
category: "jitsi"
tags: ["jitsi", "webrtc"]
---



```shell
$ git push -u origin --tags
```

收到下面的错误提示：


```shell
...

remote: GitLab: Failed to authorize your Git request: internal API unreachable                                             
To <...>                                          
 ! [remote rejected]       origin/HEAD -> origin/HEAD (pre-receive hook declined)   

...

```


<!-- more -->

从[这里](https://gitlab.com/gitlab-com/support-forum/issues/2683)找到了解决方案：

> 取消选择组设置里的 LFS 一项:

**操作步骤：**

1. 打开并编辑组：`https://<URL>/admin/groups/<GROUP>/edit`
2. 找到 `Large File Storage` 配置
3. 取消选择下面一项：

```
[ ] Allow projects within this group to use Git LFS
```