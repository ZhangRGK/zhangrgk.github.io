---
layout: post
title: Redux 还不够好
category: program
---

在已经过去的半年里，基于朴素的『新的===好的』价值观，我和同事一起在一个不算小的项目中践行 Redux 和与他对应的理念。但是，实际的使用效果比我预期的要差很多。

### Ajax

在 Redux 的原始实践中，是没有 Ajax 容身之地的，我们按照 Redux 的实践推荐把 Ajax 写到 midware 里去。这样做的优势是保持了代码的纯洁性，而缺点之一，是每一次 dispatch 都会发一个请求，不管有没有必要。

### Pure Redux

Redux 的理念中希望除了顶层组件（用于connect）之外，其他组件都是 stateless 组件，这从程序设计角度来看，是很美好的一件事。但是在实际开发的时候我们总会遇到相同的问题：

- 这个 state 是不是应该交给 Redux 管理？
- 这个 state 应该放在哪个命名空间下？

在这之中最大的问题就是：

- 这个 state 应该起什么名比较好？

命名危机在项目中将会变成一个非常可怕的问题。

### model 设计

无论是 MV* 还是 React，实际上都在践行着 MDV（数据驱动展现）的理念，这让前端设计 model 的工作变得比以往更加重要。比较合理的 model 设计应该是离散的，可以参考数据库的设计范式进行设计。

例如：

我们在做一个类似工作任务的功能时，后端限制了工作任务必须指定系统中的用户。所以正常的 model 设计应该是：

task{ userId: 1, otherProps } User{ id: 1, otherProps }

task 和 User 分别与后端同步数据，前端将 task 和 User 数据通过某种方案整合起来，形成最终与 View 绑定的 finalModel。

merge(task, User) => finalTask{ user: { id: 1, otherUserProps }, otherProps }

这个方案在后端其实已经实践了几十年，类比后端的概念大概是`service(PO1, PO2) => VO`，然而，由于 Redux 本身只能通过 Midware 支持异步，所以实际的代码中就需要作出抉择：

1. 前端 model 离散化，在 Midware 代码中判断需要获取并更新哪些 model
  - 优点: 是可以减少请求，减小 Redux Store 的体积
  - 缺点: 是前端在 Midware 中需要增加一个类似 Service 的代码层，代码的重心也会从 actions 偏向 service（这部分测试起来会很糟心），让使用 Redux 这件事变得没什么意义。
2. 前端 model 直接与 view 绑定，数据处理交给后端完成
  - 优点: 保持前端代码干净，漂亮，可测试
  - 缺点: Redux Store 变成一个有大量冗余数据的中转站，使用统一的 Store 这件事变得很没有意义

无论那种方案其实都是背离了 Redux 的思想，导致我们写出来的 Redux 代码完全看不出 Demo 中那种纯洁的美好。

### 总结

Redux 的适用范围大概是：

- 业务体量大到需要对 Actions 进行测试
- 数据结构相对复杂（越复杂就越能体现出离散化 model 的好处）
- 业务逻辑相对简单（当整合离散 model 的代码量大大超过 action 的代码量时，Redux 带来的那一点纯函数优势意义就很小了）

的应用
