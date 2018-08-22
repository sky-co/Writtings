---
title: Hexo环境搭建个人博客
date: 2017-07-21 17:14:41
categories: Others
tags:
 - Hexo
 - Blog
 - GitHub]
---

很早之前就计划搭建一个博客，但由于各方面原因（最主要还是懒），一直耽误着。终于几个月前看到同事在弄，自己也开始折腾起来。 一路跌跌撞撞，也算初步搞定。特此把中间踩过的坑记录下来，也算对后来的道友有个交代。

## 一、前期准备

### 服务器购买
---

我是用的是VPS（虚拟专用服务器）作为网站的服务器，选择了口碑较好的搬瓦工VPS，因为其价格便宜，稳定性强，并且还支持支付宝付款。
关键的关键是VPS上安装了Shadowsocks以后就可以无忧访问外网了，比隔天就被封杀的VPN靠谱多了。 **:)**

[搬瓦工官网][1]
注册完后，在Home页面有很多产品供我们选择，我之前选择的是最便宜的那款，2.99美刀一个月，如果觉得性能有瓶颈，可以考虑换个买4.99美刀一个月的，性能提升了一倍(毕竟2.99美刀的内存才256M)。
每个直达方案通道里面，有具体方案的细节，筒子们可以自己研究下再做决定。

{% qnimg 2017-07-20/2017_07_20_1.png %}

购买完成后，选择`VPS Hosting->Services->My Services`。
然后可以看到已经购买的VPS。 服务器有了，下面当然就是给你的服务器安装操作系统。 点击KiwiVM Control Panel

{% qnimg 2017-07-20/2017_07_20_2.png %}

选择`Install new OS`，我安装的是`centos-6-x86`，这个根据个人喜好选择。 装完好系统，我们可以看到自己VPS的服务器属性。

{% qnimg 2017-07-20/2017_07_20_3.png %}

然后远程连接到服务器上。 网上有很多串口连接软件，我用的是[Putty][8]。 打开后，输入服务器的IP地址和端口号。

{% qnimg 2017-07-20/2017_07_20_4.png %}

登录成功后，进入熟悉的命令行模式，至此VPS的购买就全部搞定啦！

### 域名注册
---

对于网站的访问，我们可以通过IP地址直接访问，也可以通过域名来访问。 相比之下，域名更便于记忆。 当然为了此步骤更多是为了显得更专（zhuang）业（bi）。 具体步骤如下：
域名注册网站：

- **TK**
- **Namecheap**

Tk域名，网络上最容易申请到的免费域名之一，以TK结尾，与COM、NET、CN一样同属顶级域名。 因为是免费域名，很多人用来做垃圾网站，听说百度一般不会收录。 汗！
经过网上的一番对比，最终还是选择了在Namecheap上注册域名。 购买好域名后，要配置下域名和我们的服务器IP地址的映射关系。 配置好后，可能需要等十来分钟后，才可以被DNS服务器正常解析。

{% qnimg 2017-07-20/2017_07_20_6.png %}

关于NameCheap的映射关系配置，请参考链接: [namecheap域名解析教程2017][2]
OK，恭喜你，前期工作准备完了！ 开罐可乐庆祝一下!  **:)**

## 二、Hexo环境安装

**(本机Windows部署)**
---

当初也有很多博客框架的选择摆在我面前,但是我没有犹豫选择了[Hexo][3]。主要就是看中它的迅速快，号称使用`Node.js`让上百个页面在几秒内瞬间完成渲染；同时最关键的原因在于只需要一条命令就可以完成一键部署。 终极原因是因为懒 ～
Hexo的官网上东西已经比较全了，大家动手前，可以先浏览一下官网，讲的还是很详细的关于建站这块。
闲话不多说，让我们撸起袖子，荡起双桨，二话不说开始干。


### 基本思路
---

在本地生成静态文件，把静态文件部署到VPS上，用Nginx直接做Web服务，由于hexo支持git的部署方式，从而可以实现从本地更新博客，方便快捷。


### 安装Node.js
---

在[Node.js][4]官网下载最新版，一路默认安装。

### 安装Git
---

下载[Git for windows][5]一路默认next点下去。
Git这里我们需要注意一下，需要配置一下[环境变量][6]。
然后我们需要生成SSH密钥，按下面的步骤进行。
1. 打开`C:\Users\<用户名>\.ssh`文件夹，如果没有就新建
2. 在空白处单击右键，选择`Git Bash Here`打开终端
3. 设置git用户名
```
git config --global user.email "email@example.com"
git config --global user.name "username"
```

### 生成ssh密钥
---

一路回车生成公钥和密钥，一会要用到公钥`id_rsa.pub`
`ssh-keygen -t rsa -C "email@example.com"`

{% qnimg 2017-07-20/2017_07_20_5.png %}

### 创建网站目录
---

在你电脑的任意位置创建一个文件夹（例如`C:\hexo`，下文以此代替），作为网站目录(名字可以任取)。

### 安装Hexo
---

打开`cmd`(**以管理员权限运行**)，通过`cd`命令进入`hexo`文件夹，输入`node`后执行如下命令(或者压根不用)
```
npm install -g hexo-cli
hexo init
npm install
hexo d -fg
```

运行完上述命令后,可以在`C:\hexo`文件夹下看到有以下的文件目录生成。 你可以看见hexo文件夹下有一个`themes`文件夹，这是可以自定义的，从而改变网站的呈现形式，[Hexo][7]官网也提供了一些可供选择的主题。

{% qnimg 2017-07-20/2017_07_20_7.png %}

接着启动本地hexo server
`hexo serve(或者hexo s)`
打开`http://localhost:4000` 即可看到你的站点（当然还没有发布到网络）。 如果没有响应的话，电脑重启一下，再敲入`hexo serve(或者hexo s)`
启动本地hexo server的命令。

{% qnimg 2017-07-20/2017_07_20_8.png %}

到这里，本地的网站部署和搭建就告一段落。 可以稍微休息下，准备部署VPS了。

## 三、部署Hexo
**(VPS部署)**
---

前面已经讲解过VPS的购买和系统安装，此处为`Centos 6`在`root`下的操作，关于连接VPS，Windows 用户请使用[Putty][8]（提示： Putty 中使用粘贴仅需鼠标右键）。
关于`vi`操作，按下`i`键进入编辑模式，编辑结束后按`esc`键退出，这时按`： `，并输入 `wq` ，即可。

### 安装Git
---

在Putty终端输入如下命令：
```
yum update && apt-get upgrade -y #更新内核
yum install git-core
```

### 安装nginx
---

在Putty终端输入如下命令：
`yum install nginx -y`

### 新建git用户添加sudo权限
---

在Putty终端输入如下命令：
```
adduser git
chmod 740 /etc/sudoers #改变权限值用于读写
vim /etc/sudoers
```

在vi编辑中找到如下内容：
```
## Allow root to run any commands anywhere
rootALL=(ALL) ALL
```

在下面添加一行
`git   ALL=(ALL) ALL`
保存并退出(`vi`里输入`:wq`，回车执行)后执行
`chmod 440 /etc/sudoers #改回原来的权限值`

### 创建git仓库，并配置ssh登录
---

```
su git
cd ~
mkdir .ssh && cd .ssh
touch authorized_keys
vi authorized_keys // 在这个文件中粘贴进刚刚Winodws下申请的key（在id_rsa.pub文件中）
cd ~
mkdir hexo.git && cd hexo.git
git init --bare
```
(上文中提到`hexo.git或者hexo`都可以替换成你的网站的名字)

{% qnimg 2017-07-20/2017_07_20_9.png %}

测试一下，如果在**git bash**中输入`ssh git@VPS的IP地址`,能够远程登录的话，则表示设置成功了。

### 创建网站目录并赋予git对网站目录的所有权
---

```
cd /var/www
mkdir hexo
chown git:git -R /var/www/hexo
```
(上文中提到`hexo`都可以替换成你的网站的名字)

### 配置git hooks
---

```
su git
cd /home/git/hexo.git/hooks # 如果没有hooks目录,就mkdir hooks 自己建一个目录
vim post-receive
```

输入如下内容后保存退出(`vim下输入`:wq`)

```
#!/bin/bash -l
GIT_REPO=/home/git/hexo.git #git仓库
TMP_GIT_CLONE=/tmp/hexo
PUBLIC_WWW=/var/www/hexo #网站目录
rm -rf ${TMP_GIT_CLONE}
git clone $GIT_REPO $TMP_GIT_CLONE
rm -rf ${PUBLIC_WWW}/*
cp -rf ${TMP_GIT_CLONE}/* ${PUBLIC_WWW}
```
(上文中提到`hexo.git或者hexo`都可以替换成你的网站的名字)
原理就是从`GIT_REPO`仓库中把文件拷贝到`PUBLIC_WWW`目录下发布，中间有个`TMP_GIT_CLONE`用于保存文件。
然后赋予脚本的执行权限 `chmod +x post-receive`
同时把上述的三个路径都用`chown git:git -R /路径`更改了owner

### 配置Nginx
---

`vim /etc/nginx/conf.d/hexo.conf`
插入如下代码：

```
server {
    listen         80 ;
    root /var/www/hexo;//这里可以改成你的网站目录地址，我将网站放在/var/www/hexo
    server_name example.com www.example.com;//这里输入你的域名或IP地址
    access_log  /var/log/nginx/hexo_access.log;//这里的log文件也可以根据自己网站名字替换
    error_log   /var/log/nginx/hexo_error.log;//这里的log文件也可以根据自己网站名字替换
    location ~* ^.+\.(ico|gif|jpg|jpeg|png)$ {
            root /var/www/hexo;//网站目录地址
            access_log   off;
            expires      1d;
    }
    location ~* ^.+\.(css|js|txt|xml|swf|wav)$ {
        root /var/www/hexo;//网站目录地址
        access_log   off;
        expires      10m;
    }
    location / {
        root /var/www/hexo;//网站目录地址
        if (-f $request_filename) {
            rewrite ^/(.*)$  /$1 break;
        }
    }
}
```

重启Nginx
`service nginx restart`
(如果上述操作git用户搞不定，就切换到root用户`su root`)

### 配置hexo配置文件
---

位于`hexo`文件夹下，`_config.yml`,修改`deploy`选项，改成如下
```
deploy:
- type: git
  repo: ssh://git@VPS的IP地址:/home/git/hexo.git#VPS上对应的git仓库(包含git hooks的地址)
  branch: master
- type: git
  repo: ssh://git@github.com/hexo.git#github上对应的保存静态文件的仓库
  branch: master
```

接着在hexo文件夹内，按住`shift右击`，选择在此处打开命令窗口（当然你也可以用cd命令），运行`hexo g && hexo d`，如果一切正常，静态文件已经被成功的push到了blog的仓库里，如果出现`appears not to be a git repo`的错误，删除hexo目录下的`.deploy`后再次`hexo g && hexo d`就可以了。
到这里，博客已经完全建好了。

### 更新博客
---

使用一款 MarkDown 编辑器写 Blog 。写完后将文件以 `*.md`的格式保存在本地`[网站目录]\source\_posts`中。文件编码必须为 **`UTF-8`**，这一点仅 Windows 用户需注意。
编写完后，只需要在hexo文件夹下执行`hexo g && hexo d`，博客即可更新。

### 备份
---

由于上面hexo文件夹下执行`hexo g && hexo d`执行之后, 会更新github上你的网站目录的信息, 但是markdown文件并没有保存, 保存的是转换成静态文件`html`, 所以还是需要保存下你的原文件。因此在github上建了一个仓库专门保存博客的原文件。这样更新完博客以后，只要上传一下原文件就可以高枕无忧了。  **:)**

## 四、安装中遇到问题
---

- 上传操作`hexo d`报错`spawn git ENOENT`，添加[Git环境变量][6]后可以了

- `hexo g` 报错，安装`hexo-delopyer-git`后解决。

- `hexo d` 提交到VPS网站目录里的文件并不是单独的public文件夹，检查hexo文件夹发现存在`.git`这个目录，删除`.git`文件夹即可（他是隐藏的哦）。

- `ssh git@ip`，被拒绝，是远程端口默认为`22`端口，而不是我VPS的`ssh`真正端口。
  解决方法：在`.ssh`文件夹下（也就是生成公钥的文件夹）创建`config`文件，输入如下内容：
```
Host // VPS的IP
HostName // VPS的IP
User git
Port // SSH端口
IdentityFile ~/.ssh/id_rsa
```
	{% qnimg 2017-07-20/2017_07_20_10.png %}

- `ssh -T git@github.com`是否连接上`github`
`git config --list` 查看`git config`配置

- ngix正常配置后，执行 `yum install nginx -y`。 如果失败，可能是源没有配置，在`/etc/yum.repos.d`目录下创建一个源配置文件`nginx.repo, 写入如下代码，然后再执行一次。
```
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=0
enabled=1

最后查看版本号，验证完成 `nginx -v`
```
	{% qnimg 2017-07-20/2017_07_20_11.png %}

- 用`yum install git`安装`Git`出现问题：`/bin/bash: git: command not found`
解决（修改安装命令为）：`yum install git git-svn git-email git-gui gitk -y`

- ssh配置后，ssh连接验证：`ssh: connect to host localhost port 22: Connection refused`
```
错误原因：
sshd 未安装：yum install openssh-server
sshd 未启动：chkconfig sshd on
防火墙：chkconfig iptables off(永久性生效，重启后不会复原)
端口号没有设置为22(或者设置后没有重启：service sshd restart)
```

- 使用`hero d`报错：`ERROR Deployer not found: git`
  解决：记得一定要在 blog 目录里 执行 `npm install hexo-deployer-git --save`
  {% qnimg 2017-07-20/2017_07_20_12.png %}

- 查看`node`和`npm`版本
```
node --version
npm --version
```

- warning: LF will be replaced by CRLF | fatal: CRLF would be replaced by LF
  解决办法：http://blog.csdn.net/feng88724/article/details/11600375
  遇到这两个错误，是因为Git的换行符检查功能
  对于出现一些conflic，可以reset一些，再重新更新
```
git reset --hard FETCH_HEAD
git pull origin master
```

- hexo安装成功，并且正确运行，但是localhost:4000不能访问
  解决办法：https://yq.aliyun.com/articles/58027
  遇到这个问题，是因为4000端口被占用了。如果你的电脑上不幸装了福昕阅读，恭喜你！它就是罪魁祸首....
  胳膊拧不过大腿，对于这种流氓软件，我们别无他法，只能换端口。 :(
```
启动hexo s 的时候，用这个命令，换一个端口。
hexo s -p 3600 // 随便指定一个端口号就好
```

- 添加多个key到git
  Step1 添加到github账号： 打开`C:\Users\<用户名>\.ssh`文件夹，拷贝`id_rsa.pub`里的内容，在Github账号`Settings->SSH and GPG keys->New SSH key`
  Step2 [添加到VPS][9]： 在`C:\Users\<用户名>\.ssh`文件夹下打开`Git Bash Here`, 执行`ssh-copy-id git@vps的IP`

- 运行`hexo g && hexo d`后没有触发**git hooks**解决方案
说实话这个问题困扰了我三四天了，之前都是`hexo g && hexo d`之后，跑到VPS下的发布目录（`www/hexo/`）去手动download github上的`hexo.git`仓库完成发布的。
确实不爽，于是翻了好几条网页，终于解决了。方法如下：
1. 检查你的本地public key有没有add 到github账号，并且`/home/git/.ssh/authorized_keys`里面已经加入了之前Winodws下申请的key
2. hexo文件夹下，`_config.yml`的配置语句正确如下
```
deploy:
- type: git
  repo: ssh://git@VPS的IP地址:/home/git/hexo.git#VPS上对应的git仓库(包含git hooks的地址)
  branch: master
- type: git
  repo: ssh://git@github.com/hexo.git#github上对应的保存静态文件的仓库
  branch: master
```

3. 千万记得在本地博客路径`C:\hexo\`下一定要用`**Git Bash Here**`执行命令，而不是**Windows的命令行窗口**，否则会一直报“Permission denied, please try again."
   {% qnimg 2017-07-20/2017_07_20_13.png %}
   运行`ssh -T git@github.com`，是否能正常访问github
   运行`hexo g && hexo d`，部署并提交代码到github，同时触发git hooks完成发布
   过程中会弹出一个OpenSSH的对话框要你输入git的密码，输入完毕，点击ok。搞定！

- 本地博客路径`C:\hexo\`下有个`.gitignore` 文件，主要用于`git status`忽略一些列出的文件，但是筒子们有木有发现改了该文件之后，其实并没有作用。
百思不得其解之后，搜到[某攻略][10]才豁然开朗：只改文件你列出的文件或目录在仓库中，需要执行以下命令手动移除。提交一版之后，整个世界就清净了 **:)**
```
git rm -rf --cached .
git add .
git commit -m "xxxxx"
git push origin master
```

- 为 Next 主题文末添加版权等信息
看到别人博客里的版权信息，觉得有点意思，找了很多教程，效果都不是自己想要的。只能借用下[Next作者的格式和样式][11]。
**建立基础的HTML代码**
定位到本地博客文件夹下的`themes\next\layout\_macro\post.swig`文件，这个和于`layout`下的`post.swig`的区别是前者扶着具体的`post-content`的生成，而后者是调用前者。
然后补充类似comment第三方的模块的脚本。找到post-body所在的标签，并在其后加上如下代码：然后补充类似comment第三方的模块的脚本。找到post-body所在的标签，并在其后加上如下代码：
```
<div>
 {# 表示如果不在索引列表中加入后续的HTML代码 #}
 {% if not is_index %}
	<ul class="post-copyright">
	  <li class="post-copyright-author">
		  <strong>本文作者：</strong>{{ theme.author }}
	  </li>
	  <li class="post-copyright-link">
		<strong>本文链接：</strong>
		<a href="{{ url_for(page.path) }}" title="{{ page.title }}">{{ page.permalink }}</a>
	  </li>
	  <li class="post-copyright-license">
		<strong>版权声明： </strong>
		本博客所有文章除特别声明外，均采用 <a href="http://creativecommons.org/licenses/by-nc-sa/3.0/cn/" rel="external nofollow" target="_blank">CC BY-NC-SA 3.0 CN</a> 许可协议。转载请注明出处！
	  </li>
	</ul>
 {% endif %}
</div>
```

	**增添样式**
	定位Next下的`source/css/_custom/custom.styl`,并在里面添加如下样式代码:
	```
	.post-copyright {
		margin: 2em 0 0;
		padding: 0.5em 1em;
		border-left: 3px solid #ff1700;
		background-color: #f9f9f9;
		list-style: none;
	}
	```


## 五、后记
---

以下是自己搭建过程中查找的相关链接和信息，希望对大家有帮助。
1. https://dynamicer.com/how-to-deploy-hexo-blogs-with-git-hooks-on-vps/
2. http://tiktoking.github.io/2016/01/26/hexo/
3. http://blog.csdn.net/hanhailong726188/article/details/46738929
4. http://blog.ynxiu.com/2016/hexo-next-theme-optimize.html
5. https://arcecho.github.io/2017/04/08/Hexo-Next%E4%B8%8B%E6%B7%BB%E5%8A%A0%E7%89%88%E6%9D%83%E5%A3%B0%E6%98%8E%E6%A8%A1%E5%9D%97/


## 六、编辑工具
---

因为Hexo是支持Markdown的文件，而Markdown是一种极简的『标记语言』，将文本转为`HTML`，通常为我大码农所用。 简介好使是关键！以下为一些相关链接，希望对大家有帮助。

Markdown语法
1. http://ibruce.info/2013/11/26/markdown/
2. https://maxiang.io/

Markdown编辑器
1. http://www.csdn.net/article/2014-05-05/2819623
2. https://maxiang.io/


[1]: http://banwagong.cn
[2]: http://xianhuo.org/namecheap-yumingjiexijiaocheng2016.html
[3]: https://hexo.io/zh-cn/index.html
[4]: https://nodejs.org/en/download/
[5]: https://git-scm.com/download/win
[6]: http://www.brafox.com/post/2015/%E9%A1%B9%E7%9B%AE%E7%AE%A1%E7%90%86/git-%E5%AE%89%E8%A3%85%E5%92%8C%E9%85%8D%E7%BD%AE.html
[7]: https://hexo.io/themes/
[8]: http://the.earth.li/~sgtatham/putty/latest/x86/putty.exe
[9]: https://askubuntu.com/questions/46424/adding-ssh-keys-to-authorized-keys
[10]: https://stackoverflow.com/questions/25436312/gitignore-not-working
[11]: http://notes.iissnan.com/2015/something-about-next/

