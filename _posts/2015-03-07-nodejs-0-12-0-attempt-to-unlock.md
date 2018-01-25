---
layout: post
title: NodeJS 0.12.0 Attempt to unlock
category: program
---


- mac os x 10.01
- 用pkg安装NodeJS

前天晚上更新了 Node 和 npm，今天干活的时候发现npm安装库报错:

~~~
npm ERR! Attempt to unlock /Users/ken/repo/safe/static/node_modules/gulp-jsmin, which hasn't been locked
npm ERR!
npm ERR! If you need help, you may report this error at:
npm ERR!     <http://github.com/npm/npm/issues>
npm WARN locking Error: EACCES, open '/Users/ken/.npm/_locks/gulp-coffee-2eff269b2c7fcfec.lock'
npm WARN locking     at Error (native)
npm WARN locking  /Users/ken/.npm/_locks/gulp-coffee-2eff269b2c7fcfec.lock failed { [Error: EACCES, open '/Users/ken/.npm/_locks/gulp-coffee-2eff269b2c7fcfec.lock']
npm WARN locking   errno: -13,
npm WARN locking   code: 'EACCES',
npm WARN locking   path: '/Users/ken/.npm/_locks/gulp-coffee-2eff269b2c7fcfec.lock' }
npm ERR! Darwin 14.1.0
npm ERR! argv "node" "/usr/local/bin/npm" "install"
npm ERR! node v0.12.0
npm ERR! npm  v2.5.1
~~~
{: .bash}

按照提示去 [github issues](http://github.com/npm/npm/issues) 找了一下，果然有。

解决办法是，把.npm 用户再指定一次。

~~~
sudo chown -R $(whoami) ~/.npm/_locks/
~~~
{: .bash}

解决~