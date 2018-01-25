---
layout: post
title: 真正的 websocket 断线重连
category: program
---


去年，工作中用到websocket，但是因为浏览器的实现里没有包含ping，pong的关系，没法判断websocket的连接状态，所以我萌萌哒撸了一个简单的断线重连的[小轮子](http://zhangrgk.com/websocket-duan-xian-zhong-lian/)。

结果我沾沾自喜还不到2小时，就有人发给了我一个来自github的链接
[reconnecting-websocket](https://github.com/joewalnes/reconnecting-websocket)

我简直不知道如何面对这个残忍的世界了……

跨过年来终于缓过劲来，尝试分析一下我和造出主流轮子的作者--在这里便是joewalnes--之间的差距。

以下是我分析后列出的注意事项：

#### 同理心

- 注释必须包含在阅读代码时必须的**标注**与**提醒**
- 方法的注释必须包含关于方法功能及输入、输出的说明
- 事件定义与相关的方法定义应尽量将注释放到一起，或者通过字典可以速查
- 相同功能的变量与参数名**必须**在所有作用域中保持一致，如果出现命名冲突可以作用域较小的变量前加```_```
- debug模式

#### 规范

- 开源库应该注明证书(基本就MIT了)
- 不人为的屏蔽特性(我自己写的屏蔽了一些最初我觉得用不到的东西，比如state)
- 应该处理异常

#### Javascript技能

我对于js oop, event loop, 和js基本语法中异常部分的了解，简直让我无地自容。

15年果然也要继续努力才行了……

#### 完


