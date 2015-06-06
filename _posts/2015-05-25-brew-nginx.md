---
layout: post
title:  "Error: You must `brew link pcre' before nginx can be installed"
date:   2015-05-25 01:42:01
categories: brew nginx
---
问题出现场景：
在OSX yosemite中使用brew安装nginx出现
{% highlight ruby %}
Error: You must `brew link pcre' before nginx can be installed
{% endhighlight%}

出现此报错意味着系统环境已经安装pcre但是nginx未和pcre链接上，所以先卸载pcre

{% highlight ruby %}
brew uninstall pcre
{% endhighlight%}

然后再安装nginx
{% highlight ruby %}
brew install nginx
{% endhighlight%}

成功！

因为在安装nginx的时候依赖于openssl,pcre 且需要链接安装，所以单独卸载nginx而没清理掉pcre再安装nginx就会出现未链接错误。		





参考：
http://stackoverflow.com/questions/26450085/nginx-broken-after-upgrade-to-osx-yosemite
