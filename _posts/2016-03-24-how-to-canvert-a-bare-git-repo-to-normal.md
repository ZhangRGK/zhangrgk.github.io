---
layout: post
title: 如何把 git bare repo 变成普通 repo
category: program
---

首先备份 bare repo

~~~
cp -r bare-repo.git bare-repo.git.bak
~~~

然后创建两个目录

~~~
mkdir normal-repo
mkdir normal-repo/.git
~~~

接着把 bare-repo 里的所有文件都移动到 normal-repo/.git/ 中

~~~
mv bare-repo/* normal/.git/
~~~

继续在 normal-repo 中运行这样的命令

~~~
git config --bool core.bare false
~~~

最后 checkout

~~~
git checkout master
~~~

搞定

