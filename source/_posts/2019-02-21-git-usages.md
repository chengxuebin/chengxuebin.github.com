---
layout: post
title: "Git 常用用法"
category: "git"
tags: ["git"]
---


### 1 导出 commit 修改的文件

```shell
git diff-tree -r --no-commit-id --name-only --diff-filter=ACMRT $commit_id | xargs tar -rf export.tar
```
**git diff：**

* -r $commit_id：Take a diff of the given commit to its parent(s) (including all subdirectories, not just the top directory)
* --no-commit-id --name-only：只输出文件名，不输出 SHA1
* --diff-filter=ACMRT：Only show files added, copied, modified, renamed or that had their type changed (eg. file → symlink) in this commit. This leaves out deleted files.

**tar：**

* -r：添加文件到已经压缩的文件
* -f：指定压缩文件

<!-- more -->
