---
title: Arts-Tip_Week2 - 使用Hexo和GitHub搭建博客，出现hexo -d报错解决方案
date: 2018-08-29 23:48:27
categories: Arts
tags:
 - Arts
 - Tip
 - Week2
 - Git
 - Hexo
---

## 一、背景
---
之前按照网上的教程，自己用Hexo搭建了一个博客。为了偷懒，写了一个hook的脚本，实现一行命令提交并且更新博客内容，见文稿:[Hexo环境搭建个人博客][1]。
但是之前Hexo的部署方案有问题，隔了一段时间以后发现没办法提交Github和部署到自己的博客网站了。

## 二、解决方案
---

解决方法是修改根目录下的_config.yml配置
下面是[文档][2] 中的写法
```
deploy:
type: git
repo: <repository url>
branch: [branch]
message: [message]
```

修改为
```
deploy:
- type: git
  repo: git@VPS的IP地址:/home/git/hexo.git#VPS上对应的git仓库(包含git hooks的地址)
  branch: master
- type: git
  repo: git@github.com:{yourname}/hexo.git#github上对应的保存静态文件的仓库
  branch: master
```

## 三、后记
---
以下是自己搭建过程中查找的相关链接和信息，希望对大家有帮助。
1. https://www.zhihu.com/question/38219432


[1]: http://skyhacks.org/2017/07/21/Others/UseHexoToBuildBlog/
[2]: https://hexo.io/zh-cn/docs/deployment.html#Git
