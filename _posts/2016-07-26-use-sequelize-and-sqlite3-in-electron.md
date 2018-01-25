---
layout: post
title: 在 Electron 里使用 Sequelize 和 SQLite3
category: program
---

## 安装

[先弄好一个 Electron 项目](http://electron.atom.io/docs/tutorial/quick-start/)

然后安装必要的库

~~~
npm install sequelize sqlite3
~~~

## 编译

写了一丢丢代码以后，启动 electron，出现提示：`Uncaught Error: Cannot find module sqlite3`。

看错误栈是从 sequelize 里抛出来的，搜了好一会了解到需要重新编译 sqlite3，然后我根据编译的步骤撸了脚本

~~~
cd node_modules/sqlite3 && npm run prepublish && node-gyp configure --module_name=node_sqlite3 --module_path=../lib/binding/electron-v1.3-darwin-x64 && node-gyp rebuild --target=1.3.0 --arch=x64 --target_platform=darwin --dist-url=https://atom.io/download/atom-shell --module_name=node_sqlite3 --module_path=../lib/binding/electron-v1.3-darwin-x64
~~~

其中

- `--module_path`这个参数需要根据 electron 的版本来确定，我是直接在 electron 代码里 require('sqlite3') 看错误提示，然后根据错误提示去改了版本和路径
- `--target`这个参数也需要根据 electron 的版本来确定，写官网上提供的最新版就好(对应`--dist-url`参数那个版本)
- `---target_platform`和操作系统有关

## 错误信息

- 如果出现`Module version mismatch. Expected 49, got 48`这一类的提示，应该是`--target`和你启动的`electron`版本不匹配;
- 编译之前记得要把本地环境和 electron 官方推荐的环境匹配起来，官网首页会写`Electron: 1.3.0 Node: 6.3.0 Chromium: 52.0.2743.82  V8: 5.2.361.43`这样的信息
- sqlite3直接编译会缺少一个`nan`库，需要`cd node_modules/sqlite3 && npm install nan@<version>` 后面的`version`看错误提示写

## 完