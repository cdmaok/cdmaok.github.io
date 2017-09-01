---
layout: post
title:  Note-on-Git
description: some notes on git
categories:
-blog
---

### Note
1. 如何开一个新的空分支 git checkout --orphan branch_name 然后 git reset --hard 或者 rm -rf \* (注意这里要小心)
2. 生成一个tag git tag -a tag_name -m "message"
3. 推送到远程分支 git push --tags
4. 删除本地tag git tag -D tag_name
5. 删除remote tag git push --delete origin tag_name
6. clone 特定的branch git clone -b <branch_name> <remote_repo>
7. clone 特定的tag  git clone --branch tag_name remote_repo
