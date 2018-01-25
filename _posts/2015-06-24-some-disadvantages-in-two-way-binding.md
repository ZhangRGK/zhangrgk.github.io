---
layout: post
title: 前端 MV* 数据双向绑定的缺点
category: program
---

VueJS 和 AngularJS 一样，数据是默认双向绑定的。

最开始接触的时候觉得用起来简单又方便，但是在写代码的时候隐约觉得有些不对。

后来我接触了 ReactJS 和 Polymer，感觉到单向数据绑定在设计的时候更自由，也更符合我的习惯。

但是，反正只是影响设计我也觉得没什么所谓，直到今天被坑。

> 题外话:
> VueJS 0.10 左右的版本有一个Feature，是 v-component 的 $data 域如果不是 inherit，那么 component 内部的数据变化是不会反馈到传入的参数的。
> 
> 然而在实际使用的时候并不是这样，无论是否 inherit 数据变化都会反映到绑定的组件上。
> 
> 后来在 0.11 的某个版本中取消了这一段说明。

## 代码

今天出问题的部分是在 Vue 对象中使用一个 Component ，

Component 里有异步提交(调用 API 的 save，判断标准是如果参数里带 id 就 update，如果没有 id 就 insert)，提交之后会回写数据库中的记录 id 到当前组件的数据中。

Vue 对象也需要 save 回调过程大概是这样的

~~~
vue.save(
	component.save()
	// 就这里在回调的阴沟里翻船了
	vue.clear()
)
~~~
{: .javascript}

Vue 对象里的数据也会通过点击事件更新，为了不造成污染，我在 save 之后把 Vue 对象里的数据 clear 掉。

但是，因为 id 回写是异步的，

所以我可耻的被坑了。