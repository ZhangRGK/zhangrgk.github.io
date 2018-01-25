---
layout: post
title: Sublime Text 配置
category: program
---


### 安装

想起之前有朋友推荐的编辑器，所以下下来试用一下。

下载地址:[Sublime Text 3](http://www.sublimetext.com/3)


<span style="color:red">这篇博客针对ST3</span>

<span style="color:red">因为我主要是在OS X上做开发，所以以Mac版为标准。</span>

sublime text虽然不是免费软件，但是官网也可以下载可以直接使用的版本。

收费版和免费版唯一的不同就是，免费版save的时候会不时的弹出一个购买的提示，仔细考虑了70刀的价格之后，我决定暂时还是看提示好了……


#### 基本配置

打开 `Preferences >> Settings-User `

对st做一些简单的配置

~~~
{
    "hot_exit": false,
    "ignored_packages": [
        "Vintage"
    ],
    "remember_open_files": false,
    "tab_size": 4,
    "translate_tabs_to_spaces": true
}
~~~

之后所有的插件与快捷键的配置，在这里都可以找到。

#### 安装 Package Control

按下 ```Ctrl + ` ```， 在屏幕下方的输入框里输入: 

~~~
import urllib.request,os,hashlib; h = 'eb2297e1a458f27d836c04bb0cbaf282' + 'd0e7a3098092775ccb37ca9d6b2e4b7d'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)
~~~

之后，就可以安装各种神奇的插件了。

#### 安装插件

在st底部的提示栏里确认装好了 Package Control，

然后按下 ```command + shift + p ``` 打开命令框。

输入 ``` ip ``` 搜索到 Package Control: Install Package, 按下回车，即可进入插件搜索的页面，直接输入想要安装插件的名字，搜索到想要的项目，再次按下回车，即可安装

#### 插件列表

- SideBarEnhancements : 左侧菜单栏
- Anaconda : Python集成插件
- ConvertToUTF8 : 非utf8文本自动转换
- BetterCoffeeScript : Coffee支持
- emmet : ZenCoding
- Markdown preview : markdown预览
- MarkdownEditing : markdown高亮
- Gutter Color : 显示颜色，需要先运行`brew install ImageMagick`
- terminal : 直接打开终端
- Git : 直接使用Git命令(当然要先有git)
- 各种格式化的插件就按照自己的需求选装吧

安装完成后，一个绝对不会卡的轻便IDE就完成了！

#### 后记

用了近两个月，发现st还是会渐渐的侵蚀掉系统的资源。

如果是像我一样的几个月都不重启电脑的用户可能会有些纠结。

### 完

