---
layout: post
title:  "Mac OS 10.11.x 安装ruby的正确方法"
date:   2015-04-27
categories: html5
---

这个我一定要mark一下。

我写js比较多，不是玩ruby的，我就维护维护这个博客，用到了Jekyll，然而用它需要ruby环境，装Jekyll的时候碰壁了。

好像是Mac OS 10.11.x，直接使用gem升级或者安装包 `gem install mygem` 或者 `gem update --system`，都会有下面的错误，sudo也不行：

`
ERROR:  While executing gem ... (Gem::FilePermissionError)
You don't have write permissions for the /Library/Ruby/Gems/2.0.0 directory.
`

这个问题导致你没法装包，这是Mac OS的新限制，你使用的gem是/usr/bin/下面的，错误提示的目录位置系统禁止你写入了。

解决方法是：**使用homebrew安装一个隔离的gem**

这里我不谈rvm（类似python virtual env）的一个东西，我不是玩ruby的基本用不上，我平时就用gem编下Jekyll而已。

隔离的gem不会将包在装到系统的位置，而是/usr/local/bin，因此也不会和系统的ruby冲突，PATH环境变量也不用动，因为这本来就在/usr/bin之前，echo $PATH可以看一下。

这个gem安装包的位置在 /usr/local/Cellar/，也是和系统ruby隔离的。

安装过程简单：

1. <http://brew.sh/> 上的shell跑一下
2. brew install gem
3. 好了 gem复活了 尽情gem install吧