---
title: 给Hexo添加Gitment评论插件
date: 2017-08-04 17:32:52
categories: Others
tags:
 - Next
 - Hexo
 - Gitment
---

## 前言
---

今年六一略显不同，多说正式关闭服务, 蓝后，7月6号网易云跟帖也关了。在这个多事之秋。国内的评论系统纷纷倒闭。
{% qnimg 2017-08-04/2017_08_04_1.png %}
一直想给博客加个靠谱的评论系统，只能求助于谷歌，搜索一番之后，不经意间看到了，imsun实现的一款基于 GitHub Issues 的评论系统Gitment。
`Gitment`是作者imsun实现的一款基于`GitHub Issues`的评论系统。 支持在前端直接引入, 不需要任何后端代码。 可以在页面进行登录、查看、评论、点赞等操作。同时有完整的 `Markdown / GFM` 和代码高亮支持，尤为适合各种基于 `GitHub Pages` 的静态博客或项目页面。
本篇文章仅介绍在`hexo-NexT`中添加`Gitment`评论插件, 并且增加一个点开显示评论的按钮, 对于 Gitment 的使用请参考[imsun的博客][1]。

## 一、注册github OAuth Application
---

Gitment是使用的GitHub Issues作为评论系统，在接入Gitment前，需要获得GitHub的授权，获得相应的客户端id和客户端私钥，以备站点使用。具体步骤如下图所示：
[OAuth application注册接入][2]： 在github主页头像->setting下面，填入相关信息如下
{% qnimg 2017-08-04/2017_08_04_2.png %}
注册完毕，得到owner、client_id、client_secret。
{% qnimg 2017-08-04/2017_08_04_3.png %}


## 二、添加 Gitment 插件
---

打开`/next/layout/_partials/comments.swig`文件, 在最后一个 elseif 代码块下面添加`Gitment`相关的内容。
```
{% elseif theme.changyan.appid and theme.changyan.appkey %}
   <div id="SOHUCS"></div>
{% elseif theme.gitment.enable %}
    <div onclick="showGitment()" id="gitment_title" class="gitment_title">显示 Gitment 评论</div>
    <div id="container" style="display:none"></div>
    <link rel="stylesheet" href="https://imsun.github.io/gitment/style/default.css">
    <script src="https://imsun.github.io/gitment/dist/gitment.browser.js"></script>
    <script>
    const myTheme = {
      render(state, instance) {
        const container = document.createElement('div');
        container.lang = "en-US";
        container.className = 'gitment-container gitment-root-container';
        container.appendChild(instance.renderHeader(state, instance));
        container.appendChild(instance.renderEditor(state, instance));
        container.appendChild(instance.renderComments(state, instance));
        container.appendChild(instance.renderFooter(state, instance));
        return container;
      }
    }

    function showGitment() {
      $("#gitment_title").attr("style", "display:none");
      $("#container").attr("style", "").addClass("gitment_container");
      var gitment = new Gitment({
        id: window.location.pathname,
        theme: myTheme,
        owner: '{{ theme.gitment.owner }}',
        repo: '{{ theme.gitment.repo }}',
        oauth: {
          client_id: '{{ theme.gitment.client_id }}',
          client_secret: '{{ theme.gitment.client_secret }}'
        }
      });
      gitment.render('container');
    }
    </script>
{% endif %}
```
然后打开`NexT`主题的`_config.yml`文件, 在评论区域添加以下代码：
```
# Gitment comments
gitment:
  enable: true
  owner: xxxx #your github ID
  repo: xxxx #the name of repo to store comments()
  client_id: xxxx #your client ID
  client_secret: xxxx #your client
```
我的配置信息如下：
{% qnimg 2017-08-04/2017_08_04_4.png %}


## 三、“显示 Gitment 评论” 的按钮样式
---

在 next/source/css/_common/components 目录中新建一个 gitment.styl 的 css 样式文件, 复制以下代码
```
.gitment_title:hover {
    color: #fff;
    background: #0a9caf;
    background-image: initial;
    background-position-x: initial;
    background-position-y: initial;
    background-size: initial;
    background-repeat-x: initial;
    background-repeat-y: initial;
    background-attachment: initial;
    background-origin: initial;
    background-clip: initial;
    background-color: rgb(10, 156, 175);
}
.gitment_title {
    border: 1px solid #0a9caf;
    border-top-color: rgb(10, 156, 175);
    border-top-style: solid;
    border-top-width: 1px;
    border-right-color: rgb(10, 156, 175);
    border-right-style: solid;
    border-right-width: 1px;
    border-bottom-color: rgb(10, 156, 175);
    border-bottom-style: solid;
    border-bottom-width: 1px;
    border-left-color: rgb(10, 156, 175);
    border-left-style: solid;
    border-left-width: 1px;
    border-image-source: initial;
    border-image-slice: initial;
    border-image-width: initial;
    border-image-outset: initial;
    border-image-repeat: initial;
    border-radius: 4px;
    border-top-left-radius: 4px;
    border-top-right-radius: 4px;
    border-bottom-right-radius: 4px;
    border-bottom-left-radius: 4px;
}
.gitment_title {
    display: inline-block;
    padding: 0 15px;
    padding-top: 0px;
    padding-right: 15px;
    padding-bottom: 0px;
    padding-left: 15px;
    color: #0a9caf;
    cursor: pointer;
    font-size: 14px;
}
```
然后打开同目录中的 components.styl 文件, 找个顺眼的位置添加一句 
```
@import "gitment";
```


## 四、为每篇博文初始化评论系统
---

由于gitment的原理是为每一遍博文以其URL作为标识创建一个`github issue`，对该篇博客的评论就是对这个issue的评论。因此，我们需要为每篇博文初始化一下评论系统， 初始化后，你可以在你的github上会创建相对应的issue。
前面的步骤完成，重新部署网站后，你就可以在你的博文页下面看到一个评论框，还有看到以下错误`Error: Comments Not Initialized`，提示该篇博文的评论系统还没初始化。
- 点击**Login with GitHub**后，使用自己的github账号登录后， 就可以在上面错误信息处看到一个**Initialize Comments**的按钮。
	(注意: 由于要求回调URL和当前地址一样，故第2步不能在本地调试， 需把代码先上传部署到服务器以后再调试，即`hexo s`是看不到效果的，只有`hexo g && hexo d`才行)。
- 点击**Initialize Comments**按钮后，就可以开始对该篇博文开始评论了， 同时也可以在对应的github仓库看到相应的issue。
	{% qnimg 2017-08-04/2017_08_04_5.png %}


## 五、安装中遇到问题
---

- 获取对应数据ID
	在配置owner的时候，可以在浏览器中输入：`https://api.github.com/users/GitHub用户名`，来获取对应数据id。
- 配置主题文件_config.yml时，格式错误，比如gitment前有空格也会造成代码生成问题。
- `Error: Not Found`问题，repo库填写问题，详见[gitment issues 18][3]。
	Not Found问题，基本上是因为`NexT`主题的`_config.yml`文件里，配置信息不对。一般是Github ID填错，或者repo填错(git评论仓库的名称，不是git仓库地址)。
- 其他问题，参见[作者项目的Issue][4]。


## 四、后记
---

以下是自己搭建过程中查找的相关链接和信息，希望对大家有帮助。
1. https://github.com/imsun/gitment
2. https://imsun.net/posts/gitment-introduction/
3. https://meesong.github.io/StaticBlog/2017/NexT+Gitment/
4. https://zonghongyan.github.io/2017/06/29/201706292034/


[1]: https://imsun.net/posts/gitment-introduction/
[2]: https://github.com/settings/applications/new
[3]: https://github.com/imsun/gitment/issues/18
[4]: https://github.com/imsun/gitment/issues
