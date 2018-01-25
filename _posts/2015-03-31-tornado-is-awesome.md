---
layout: post
title: Tornado 有很多黑科技
category: program
---


### 序言

开会的时候太无聊，所以就看看Tornado的API。竟然发现了很多Tornado的轻松又简便的设置，可以完成一些很麻烦又必要的功能。

###### HTML转义

tornado可以通过配置的方式将所有请求的值转义
~~~
application = tornado.web.Application(/*url_mapping*/,
autoescape='xhtml_escape')
~~~
{: .python}

也可以配置在某个模板中

~~~
{% raw %}
{% autoescape xhtml_escape %}
{% endraw %}
~~~
{: .python}

如果只有一部分需要用到，可以直接在代码里调用

~~~
escaped_str = tornado.escape.xhtml_escape(str)
~~~
{: .python}

###### 客户端验证

tornado可以通过配置，在cookies中塞入一个参数，用以验证当前请求是否是从客户端发出。
~~~
application = tornado.web.Application(/*url_mapping*/, 
"xsrf_cookies": True)
~~~
{: .python}

在setting中配置了之后，在需要验证的模板中引用ui_module
~~~
{% raw %}
{% module xsrf_form_html() %}
{% endraw %}
~~~
{: .python}

然后，如果cookie中不包含```_xsrf```参数，或者参数内容无法与后端匹配的请求，就会毫不留情的block掉。

如果对```_xsrf```有一些自己的验证，可以重写```RequestHandler.check_xsrf_cookie()```方法