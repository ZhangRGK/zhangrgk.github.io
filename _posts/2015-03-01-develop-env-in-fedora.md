---
layout: post
title: 把 Fedora 打造成我的开发环境(然而我用os x)
category: program
---


#### 安装 chrome
~~~
sudo yum install -y vim
sudo vim /etc/yum.repos.d/google_chrome.repo
~~~
{: .bash}

~~~
[google-chrome-64]
name=google-chrome - 64-bit
baseurl=http://dl.google.com/linux/chrome/rpm/stable/x86_64
enabled=1
gpgcheck=1
gpgkey=https://dl-ssl.google.com/linux/linux_signing_key.pub
~~~
{: .bash}

~~~
sudo yum install -y google-chrome-stable
~~~
{: .bash}

p.s. 当然要翻墙，不然连不上google的验证服务器。

#### 安装fastestmirror

~~~
sudo yum install -y yum-plugin-fastestmirror
~~~
{: .bash}


#### 删除没用的库
~~~
sudo yum -y remove abrt* calligra* ktp*
~~~
{: .bash}


#### 添加源
~~~
sudo rpm -ivh http://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-stable.noarch.rpm http://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-stable.noarch.rpm
~~~
{: .bash}


按照 [这里](http://mirrors.163.com/.help/fedora.html)说的添加网易源

~~~
sudo yum makecache
~~~
{: .bash}


#### 安装 rime

~~~
sudo yum install -y ibus-rime
~~~
{: .bash}


~~~
ibus-daemon -dxr
~~~
{: .bash}


#### 安装 Node

yum源貌似已经没有跟进新版的Node了，所以用源码安装

~~~
cd /usr/local/src/
wget https://nodejs.org/dist/v5.3.0/node-v5.3.0.tar.gz
tar xvf node-v5.3.0.tar.gz
cd node-v5.3.0
./configure
make && make install
cp /usr/local/bin/node /usr/sbin/
~~~
{: .bash}


#### 安装 Git

~~~
sudo yum install git
~~~
{: .bash}


#### 安装 pip

~~~
sudo yum install python-pip
~~~
{: .bash}


#### 安装python库

~~~
sudo pip install tornado
~~~
{: .bash}

其他的按照具体要求再慢慢搞定了

#### 安装 sublime text 3

~~~
#!/bin/sh

# Sublime Text 3 install with Package Control

curl -o ~/st3.tar.bz2 http://c758482.r82.cf2.rackcdn.com/sublime_text_3_build_3065_x64.tar.bz2
if tar -xf ~/st3.tar.bz2 --directory=$HOME; then
  sudo mv ~/sublime_text_3/ /usr/local/
  sudo ln -s /usr/local/sublime_text_3/sublime_text /bin/subl
fi
rm ~/st3.tar.bz2


# Package Control - The Sublime Text Package Manager: https://sublime.wbond.net
curl -o ~/Package\ Control.sublime-package https://sublime.wbond.net/Package%20Control.sublime-package
sudo mv ~/Package\ Control.sublime-package /usr/local/sublime_text_3/Packages/


# Add to applications list
cat << EOF > ~/.local/share/applications/sublime.desktop
[Desktop Entry]
Name=Sublime Text
Exec=subl %F
MimeType=text/plain;
Terminal=false
Type=Application
Icon=/usr/local/sublime_text_3/Icon/128x128/sublime-text.png
Categories=Utility;TextEditor;Development;
EOF


echo ""
echo "Sublime Text 3 installed successfully!"
echo "Run with: subl"
~~~
{: .bash}

把以上内容保存成 install_st3.sh，然后运行
~~~
chmod +x install_st3.sh
sh ./install_st3.sh
~~~
{: .bash}


sublime text 3 的配置可以参考[这篇博客](http://zhangrgk.com/xue-bu-hui-vimde-ren-zen-yang-bai-tuo-idede-fan-nao/)

#### 安装PG

~~~
sudo yum install postgresql postgresql-server pgadmin3 postgresql-contrib
~~~
{: .bash}
