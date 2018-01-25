---
layout: post
title: Python 动态加载模块
category: program
---


写工具的时候遇到一个问题，需要动态引入一个module，然后判断module中定义的class是否属于```peewee.Model```的子类。

用 importlib 引入

~~~
path = 'custom_module.py'
custom_module = importlib.import_module(path)
~~~
{: .python}

尝试了python提供的反射属性，都没能获取到class对象，所以改用```dir```配合```getattr```的方式来获取到module中所有东西:

~~~
for attr_name in dir(model_module):
	attr = getattr(model_module, class_name)
~~~
{: .python}

但是接下来判断是否属于```peewee.Model```子类的时候就出了问题，

~~~
if issubclass(attr, Model) and attr.__name__ != 'Model':
    attr._meta.database = db
    tables.append(attr)
~~~
{: .python}

会抛出异常

~~~
Traceback (most recent call last):
  File "define_model.py", line 28, in <module>
    defineModels("custom_model")
  File "define_model.py", line 20, in defineModels
    if issubclass(attr, Model) and attr.__name__ != 'Model':
  File "/Library/Python/2.7/site-packages/peewee.py", line 208, in __getattr__
    return self[attr]
KeyError: '__bases__'
~~~
{: .bash}

查了一些资料，是因为```attr```当中有一个```dict```,所以在判断issubclass的时候抛出了异常。

纠结了半天最后还是同事提醒直接把异常吞了，最终完成。

~~~
model_module = importlib.import_module(model_path)
tables = []
	for attr_name in dir(model_module):
	attr = getattr(model_module, attr_name)
	try:
		if issubclass(attr, Model) and attr.__name__ != 'Model':
			attr._meta.database = db
			tables.append(attr)
	except Exception:
		continue
~~~
{: .python}

#### 完