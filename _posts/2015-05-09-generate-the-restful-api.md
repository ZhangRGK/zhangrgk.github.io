---
layout: post
title: 根据关系型数据库自动生成 RESTFUL API
category: program
---


#### 起因

每次想要挖新坑做点什么的时候，总会每次写重复的后端代码太无聊而放弃。有一天看到有在线的直接配置生成 Restful API 的服务，但是试用过以后总是觉得太麻烦了，有各种各样的配置比写后端代码还麻烦。所以干脆就尝试写一个服务自用吧。

#### 需求

- 只需要描述对象之间的关系(1:1,1:n,n:n)
- 符合Restful API设计的基本规则
- 能够验证客户端

#### 可行性

我需要确定 Restful API 的特性能够和数据库特性1-1对应:

- 把数据资源化: schools 表中的内容被发布到 /school
- 数据之间有关联性: teachers表与schools表为1对多关系，那么可以通过访问 /schools/1/teachers 获取到id为1的学校里所有老师的信息
- get/post/put/delete/: select/insert/update/delete
- 过滤信息: /teachers?gender=male 搜索gender=male的所有老师

还有一些由代码来实现的能力:

- 状态码
- Accept
- 错误信息
- Hypermedia API