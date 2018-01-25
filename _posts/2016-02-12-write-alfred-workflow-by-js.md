---
layout: post
title: 用 JavaScript 写 Alfred Workflow
category: program
---

团队里推荐用 [Contributing to AngularJS](https://github.com/angular/angular.js/blob/master/CONTRIBUTING.md) 写 CommitMessage，要求略微复杂了一些，为了帮助记忆（偷懒），我用 js 写了一个 workflow。

## Alfred Workflow

我最先尝试用 Alfred 支持的 JS runtime 写，但是不能用 ES2015 的语法总是很不爽，所以我直接改成用 base 运行 nodejs 来执行

~~~
/usr/local/bin/node <<-'CODE'
require("./index")("{query}");
CODE
~~~
{: .bash}

其中 {query} 是 alfred 传到 代码中的参数

对应的 index 代码如下

~~~

"use strict";
var util = require('./util.js');

module.exports = function(query) {
	let params = query.split(' ');
	let array;
	if(params.length === 1) {
		array = util.headerHelper.loadType(query);
	} else if(params.length === 2) {
		array = util.headerHelper.appendScope(params[0], params[1]);
	} else {
		array = util.headerHelper.appendSubject(params)
	}
	console.log(util.genAlfredXML(array));
}
~~~
{: .javascript}

alfred 会捕捉控制台输出，如果符合约定的 xml 格式，就作为结果返回

util代码太长就不贴了，alfred 的功能是分别输入 type(scope): subject ，然后生成一段字符串放到剪贴板里，提示我写 commit message，

格式如下：

~~~
type(scope): subject

# <body>
# 应该说明代码变动的动机，以及与以前行为的对比

# <footer>
# 说明不兼容修改或者关闭 issues
~~~
{: .bash}