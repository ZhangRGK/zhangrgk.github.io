---
layout: post
title: 基于 Vue.js 与 Tornado Template 的前端模块化方案
category: program
---


#### 技术背景

- Python
- tornadoweb
- Vuejs

#### 要求

- 复用分为三种粒度：模块，组件，代码段
- 模块与组件的重用支持覆盖
- 尽可能对设计师友好

#### 思路

1. 代码段复用HTML语法支持（AMD），不需要考虑太多；
1. 组件复用基本上是遵循Vuejs的逻辑（也是MVVM的方式），以Directive，Filter和Component的方式复用。
1. 模块复用使用 tornado ui module 。将一部分后端代码与独立的html，js，css打包成一个模块使用。

但是，还有几个问题没有解决

1. HTML并没有include，extends的能力，无法做到灵活的替换组件中部分内容；
1. 设计师必须具有Python和tornadoweb知识，才能使用模块与组件；

最终没有找到能完美满足要求的方案，妥协后决定引入 tornadoweb template 模块来解决include和extends的问题。而对设计师技能要求的问题只能通过尽量简化项目结构和技术之外的方式来解决。

#### 解决方案

###### 模块复用

使用tornadoweb ui module 来完成；

用之前需要把 ui module 库加入到 PYTHONPATH 环境变量中。

定义(仅作参考，以官方最新API为准)
~~~
class MyModule
    def render(self, entry, show_comments=False):
        '''
        指定渲染的模板和参数
        '''
        return self.render_string("my_module.html", **params)
    def js_file(self):
    	'''
        指定js文件，重写css_file也可以指定css文件
        embedded_css和embedded_js可以直接写css和js代码，但是太奇葩了还是不用的好
        '''
        return "path_to_js.js"
~~~
{: .python}

使用(在 tornado template 中)


~~~
import MyModule
~~~
{: .python}

~~~
{% raw %}
{% module MyModule(params=params) %}
{% endraw %}
~~~
{: .html}
###### 组件复用

因为使用了tornado template，而且 tornado不能指定多个template目录，所以需要将组件库引入到template目录中；

我最终选择的是git submodule

定义:my_component.html
~~~
{% raw %}
{% block html %}
<!-- html tags -->
{% end %}
{% block css %}
// css
{% end %}
{% block js %}
// js
{% end %}
{% endraw %}
~~~
{: .html}
注意：如果定义中使用了第三方库，需要自己处理依赖与加载顺序的问题。

使用:
~~~
{% raw %}
{% include lib/my_component.html %}
{% endraw %}
~~~
{: .html}

###### 代码段

除了AMD引入之外，也可以通过前两种方式引入，

但是，tornado ui module 默认是将js添加到html末尾，如果有需要修改引入位置，可以参考这一篇博客:

[tornador UI module 修改 js 嵌入位置](http://bigzhu.org/blog/tornador%20UI%20module%20%E4%BF%AE%E6%94%B9%20js%20%E5%B5%8C%E5%85%A5%E4%BD%8D%E7%BD%AE)

#### 完
