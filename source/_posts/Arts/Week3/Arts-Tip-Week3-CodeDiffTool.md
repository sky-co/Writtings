---
title: Tip_Week3 - Code Diff工具分享
date: 2018-09-9 23:48:27
categories: Arts
tags:
 - Arts
 - Tip
 - Week3
 - Git
 - Linux
 - Windows
 - Meld
 - Beyond Compare
---

## Linux
---
Linux下比较常用的代码比较工具是Meld。Meld 是一个可视化的文本差异比较工具，它可以跟多个版本控制系统集成。
安装也极其便利：
```
sudo apt-get install update

sudo apt-get install meld
```
使用：
绿色表示没有的，蓝色表示不同的。
既然已经知道了不同之处，接下来就是合并了。
看到蓝色和绿色颜色区域的“->”和“<-”箭头没，只需点一下，即可。
其他的一些例子和使用方法，可以参考： http://linux-commands-examples.com/meld

更多的一些Linux下的代码比较工具： https://www.fossmint.com/best-diff-merge-tools-for-linux/

## Windows
---
Windows下主要常用的是Beyond Compare，不过这个软件是付费的。但是相比同类产品还是比较便利。
对于Windows下Git的用户来说，使用图形客户端进行差异比较和合并冲突比较直观，因此使用Beyond Compare作为git的比对与合并工具。

### 配置
打开 C:\Users\Administrator\.gitconfig 文件，添加如下内容
```
[diff]
    tool = bc4
[difftool "bc4"]
    cmd = "\"c:/Program Files/Beyond Compare 4/BComp.exe\" \"$LOCAL\" \"$REMOTE\""
[merge]
    tool = bc4
[mergetool "bc4"]
    cmd = "\"c:/Program Files/Beyond Compare 4/BComp.exe\" \"$LOCAL\" \"$REMOTE\" \"$BASE\" \"$MERGED\""
```
(其中bc4什么的根据你安装的/Beyond Compare版本来，以上举例是Beyond Compare4)

也可以通过命令行来设置，命令如下：
```
#difftool 配置
git config --global diff.tool bc4
git config --global difftool.bc4.cmd "\"c:/program files (x86)/beyond compare 4/bcomp.exe\" \"$LOCAL\" \"$REMOTE\""

#mergeftool 配置
git config --global merge.tool bc4
git config --global mergetool.bc4.cmd "\"c:/program files (x86)/beyond compare 4/bcomp.exe\" \"$LOCAL\" \"$REMOTE\" \"$BASE\" \"$MERGED\""
git config --global mergetool.bc4.trustExitCode true
```
设置好后，使用命令是 git difftool 而不是之前的 git diff 了。

使用
差异比较
```
git difftool <filename>
```

合并冲突
```
git mergetool
```

参考
[Windows下使用Beyond Compare作为git的比对与合并工具][1]
[Git下的冲突解决][2]
[git difftool说明文档][3]
[浅析Beyond Compare复制文件的三种方式][4]


[1]: http://skyhacks.org/2017/07/21/Others/UseHexoToBuildBlog/
[2]: https://blog.csdn.net/mad1989/article/details/16885569
[3]: http://git-scm.com/docs/git-difftool.html
[4]: http://www.beyondcompare.cc/jiqiao/wenjianjia-fuzhi.html
