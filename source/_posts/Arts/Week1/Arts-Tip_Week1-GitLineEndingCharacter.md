---
title: Tip_Week1 - Git 换行符（LF和CRLF）问题解决
date: 2018-08-20 00:26:35
categories: Arts
tags:
 - Arts
 - Tip
 - Week1
 - Git
---

## 一、背景
---
在各操作系统下，文本文件所使用的换行符是不一样的。UNIX/Linux 使用的是 0x0A（LF），早期的 Mac OS 使用的是0x0D（CR），后来的 OS X 在更换内核后与 UNIX 保持一致了。但 DOS/Windows 一直使用 0x0D0A（CRLF）作为换行符。Git提供了一个“换行符自动转换”功能。这个功能默认处于“自动模式”，当你在签出文件时，它试图将 UNIX 换行符（LF）替换为 Windows 的换行符（CRLF）；当你在提交文件时，它又试图将 CRLF 替换为 LF。Git 的“换行符自动转换”功能听起来似乎很智能、很贴心，因为它试图一方面保持仓库内文件的一致性（UNIX 风格），一方面又保证本地文件的兼容性（Windows 风格）。但遗憾的是，这个功能是有 bug 的，而且在短期内都不太可能会修正。

## 二、解决方案
---
### Git设置
```
git config --global core.autocrlf false
git config --global core.safecrlf true
```

### AutoCRLF
**提交时转换为LF，检出时转换为CRLF**
`git config --global core.autocrlf true`

**提交时转换为LF，检出时不转换**
`git config --global core.autocrlf input`

**提交检出均不转换**
`git config --global core.autocrlf false`

### SafeCRLF
**拒绝提交包含混合换行符的文件**
`git config --global core.safecrlf true`

**允许提交包含混合换行符的文件**
`git config --global core.safecrlf false`

**提交包含混合换行符的文件时给出警告**
`git config --global core.safecrlf warn`

### IDE设置使用UNIX换行符
IDEA的设置File -> Settings
Editor -> Code Style
Line separator (for new lines) ，选择：Unix and OS X (\n)

对已使用Windows换行符的文件，可以使用Sublime Text打开，
View->Line Endings，选Unix，保存；

## 三、后记
---
以下是自己搭建过程中查找的相关链接和信息，希望对大家有帮助。
1. https://www.cnblogs.com/zjoch/p/5400251.html
2. https://blog.csdn.net/w6248117/article/details/76177103
3. https://blog.csdn.net/guodengh/article/details/8630888
4. http://kuanghy.github.io/2017/03/19/git-lf-or-crlf


