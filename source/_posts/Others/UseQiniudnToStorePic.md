---
title: 使用七牛为Hexo存储图片
date: 2017-08-02 17:53:29
categories: Others
tags:
 - Hexo
 - Qiniu
---

搭建完博客之后一直使用本地图片（相对路径），这样不是很方便，毕竟事关逼格。网上搜索了一把，貌似七牛云做图床评价挺高。说干就干，咱也尝试着使用七牛作为博客的图床了。

## 一、七牛是什么
---

[七牛][1] 是一个云存储服务商，注册并实名认证之后，你将免费享有 10GB 存储空间，每月 10GB 下载流量、100 万次 GET 请求、 10 万次 `PUT/DELETE` 请求。这对于一个博客来说，完全足够了。
{% qnimg 2017-08-02/2017_08_02_1.png %}

## 二、为什么选用七牛
---

**与直接部署到站点相比：**
- 访问速度更快 （特别是对比部署到github的情况）。
- 对个人用户而言空间和流量都十分阔绰。 Gitcafe有 512Mb 的总空间限制，Github虽没有找到类似的说明，但是在其上托管过多媒体资源也不是太合适的做法。而在七牛，你甚至可以通过邀请好友来增加每月下载流量（我的[邀请链接][3]），每成功邀请一名朋友将增加5GB的下载流量，最高上限可达40GB。
- 如果你爱折腾，还可以玩出更多花样3：
- 防盗链；
	对图片、音视频等在线处理，不必自己做重复工作。通过调用接口，七牛可以帮你：加水印、自定义裁剪区域、旋转、调整图片质量、转换格式；
	分析下载量等详细数据，挖掘用户行为。

**与其他上传站点相比：**
- 更稳定：七牛毕竟是老牌服务商，数据的安全性和稳定性都较有保证。如果上传到什么小网站，哪天发现图片都打不开了才是人间惨剧~
- 更灵活：配合 hexo-qiniu-sync 插件4，就算是七牛以后更改了空间域名，也不用一篇一篇地去改外链地址，只要在配置文件中修改外链前缀即可。
- 更方便：配合 hexo-qiniu-sync 插件，不必”上传→复制链接→引用外链“，直接按照本地路径引用，然后键入一条命令同步到七牛就好。会自动识别哪些是新增、修改的文件需要上传，哪些是原有的文件不需改动，十分方便。

**当然，虽然七牛有很多优点，但是有一些不足：**
- 在线文件管理不支持目录。
- 本地删除文件，云端不能同步删除文件。
尽管仍有欠缺，私以为七牛依旧是现在个人博客最佳的资源储存服务。

## 三、七牛存储空间的设置
---

1. [申请七牛账号][4]
2. 选择**对象存储**创建存储空间
	{% qnimg 2017-08-02/2017_08_02_2.png %}
	配置相关参数
	{% qnimg 2017-08-02/2017_08_02_3.png %}
3. 点击页面右上角头像→进入”个人中心“→选择”密钥管理“→复制当前使用中的`AK`和`SK`(待会设置插件时会用到)。
    {% qnimg 2017-08-02/2017_08_02_4.png %}


## 四、设置 hexo-qiniu-sync 插件
---

前人栽树，后人乘凉。有了插件[**hexo-qiniu-sync**][5]，可以不用手动上传文件到七牛，插件会自动帮你将本地目录的文件同步到七牛之前设定的存储空间下。
### 安装插件 
在hexo主目录下运行以下命令进行安装
```
npm install hexo-qiniu-sync --save
```

### 添加插件配置信息
在hexo主目录下找到配置文件 `_config.yml`, 加入以下配置信息：
```
#七牛云存储设置
qiniu:
  offline: false
  sync: true
  bucket: bucket_name
  secret_file: sec/qn.json or C:
  access_key: AccessKey
  secret_key: SecretKey
  dirPrefix: static
  urlPrefix: http://bucket_name.qiniudn.com/static
  up_host: http://upload.qiniu.com
  local_dir: static
  update_exist: true
  image: 
    folder: images
    extend: 
  js:
    folder: js
  css:
    folder: css
  audio:
    folder: audio
```

**几个注意点:**
- **bucket** ：修改为你刚才申请的七牛空间名称
- **access_key 、 secret_key** : 上传密钥`AccessKey、SecretKey`。即3 第3步复制的AK和SK。
- **urlPrefix** : 七牛空间地址的前缀。 **重要！必填！** 
  因为现在七牛已经取消了二级域名，外链前缀类似设置中的 http://7xqb0u.com1.z0.glb.clouddn.com ，插件不能根据 bucket 自动生成外链前缀了。你可以进入自己创建的空间在域名设置中看到自己的七牛测试域名，也可以查看一个已上传的文件外链地址，确认前缀无误。这个参数的设置是保证解析成网页文件时，外链地址正确的关键之一！
	{% qnimg 2017-08-02/2017_08_02_5.png %}
- 不要在plugins下面添加hexo-qiniu-sync的配置项，否则会导致错误，[详见][6] 
```
# 错误示例
plugins:
  - hexo-qiniu-sync
```
配置参数说明：
- **offline 参数：**
如果要使用同步到七牛空间的静态资源，请设置为 false。如果只想浏览在本地的静态资源文件，则设置为 true。
- **secret_file 参数： **
可有可无，貌似有了这个之后上面的两个参数就不需要配置了。这个路径是需要你自己建立一个json的文件存储上面的秘钥，然后把文件的路径写在这里就好
- **sync 参数：**
如果你想关掉七牛同步，将此参数设置为false即可，不过一般用不到修改这个参数。
- **dirPrefix 参数：**
将资源上传到七牛空间内这个目录下(说是目录会容易理解点)，默认为 `static` 目录。
可设置为空。该参数会影响外链的地址，如果设置为非空值，例如默认值 `static` ，则 `urlPrefix` 为保持一致需加上目录后缀 `/static` ，改为 `http://bucket_name.qiniudn.com/static` 。
(外链地址http://bucket_name.qiniudn.com，替换成自己地址)
- **urlPrefix 参数：**
七牛空间地址的前缀，会按默认格式自动生成地址，所以此参数可省略。
默认格式为 `http://bucket_name.qiniudn.com/static` ，如果你的七牛空间使用自定义域名或域名不是此格式的，请配置此参数。
当你设置了 `dirPrefix` 参数后，如`static`。则此`url`地址必须加上目录后缀 `/static` ， 即`http://bucket_name.qiniudn.com/static`，否则静态资源将无法访问。
如果没有设置 `dirPrefix` 参数，url地址不必加上目录后缀， 即`http://bucket_name.qiniudn.com`。
(外链地址http://bucket_name.qiniudn.com，替换成自己地址)
- **local_dir 参数：**
只填写一个目录名称即可，建立在hexo博客的主目录，不需要使用子目录。
当你在配置中填写好文件夹后，运行hexo时，会自动建立对应的目录。
如果你了解hexo文件夹的关系，担心这样会导致离线模式不能查看到图片，我可以告诉你你不需要担心这个问题。
在你以离线模式运行时，会自动使用软连接/联接方式帮你建立文件夹的引用，可以让你的离线浏览节省一倍的空间。
- **update_exist 参数：**
如果你的静态文件会进行修改或替换，并需要更新七牛空间上原先上传的文件，则设置为 true 。
是否更新空间上已上传的文件，是按照对比文件大小是否相同或者本地文件在上传到七牛空间之后进行过修改的规则进行判断的。
- **image/js/css ：**
子参数folder为不同静态资源种类的目录名称，一般不需要改动
- **image : extend 参数：**
这是个特殊参数，是文章内使用 qnimg 标签引用图片的默认图片处理操作。请参考 七牛开发者中心-图片处理。
可以使用 基本图片处理（imageView2）、高级图片处理（imageMogr2）、图片水印处理（watermark） 这三个图片处理接口，多个接口内容之间用 | 间隔。
例如 `?imageView2/2/w/500` 即生成宽度最多500px的缩略图。

以下是我的配置文件
{% qnimg 2017-08-02/2017_08_02_6.png %}

这样你在 `local_dir` 的`image命名`的文件夹中添加一张图片，之后运行`hexo（如hexo s）`，图片就可以在你的七牛存储空间的内容管理中看见了。
你会看到图片的外链URL就是`http://your_site/your_static(你的目录-dirPrefix参数的配置)/images/yourimage.png`。就是`域名+自定义的目录名+images（配置文件中image中folder指定的文件）+ 图片名`
{% qnimg 2017-08-02/2017_08_02_7.png %}


## 五、引用七牛资源
---

### 引用图片
图片的引用是最典型的情况。让我们来看最简单的情况：如果你想引用储存在 `local_dir/images` 下的图片 `demo.jpg` ，只需在文章中插入：
```
{% qnimg demo.jpg %}
```
生成站点时将被解析为：
```
<img src="http://7xqb0u.com1.z0.glb.clouddn.com/images/demo.jpg">
```
这里，我们总结一下生成的图片外链地址规则。我们把引用实例概括为：
```
{% qnimg ImgFile %}
```
则生成的图片外链地址规则为`urlPrefix + / + image.folder + / + ImgFile`。
因此，要想正确的引用外链，一定要注意每一个步骤的设置。如果最后外链不能正常显示，首先对比一下网站博客引用的地址和七牛的外链地址是否一致，如果不一致，则对照外链地址的生成规律，检查文件存放路径、 urlPrefix 、 dirPrefix 、 image.folder 设置是否正确。

### 图片引用的高阶版
高级用法的通用模版：
```
{% qnimg imageFile [attr1:value1] ['attr2:value21 value22 ...'] [extend:... | normal:yes] %}
```
[] 表示可选项，按需添加，也可以不写
| 表示二选一
extend:... 图片处理参数，表示对图片进行某种特定处理5，多个处理之间用 | 间隔。
normal:yes 表示使用原图，忽略 _config.yml 文件中的 image.extend 设置
实例 图片的本地路径为 cdn/images/test/demo.png ：
```
{% qnimg test/demo.png title:标题 alt:说明 'class:class1 class2' extend:?imageView2/2/w/600 %}
```
解析为：
```
<img title="标题" alt="说明" class="class1 class2" src="http://7xqb0u.com1.z0.glb.clouddn.com/images/test/demo.png?imageView2/2/w/600">
```
其中 ?imageView2/2/w/500 代表生成宽度最多500px的缩略图5。如果经常使用某种图片效果，可以在其七牛创建处理样式，以后直接将 extend 参数设置为 分隔符 + 样式名 就好。例如设置的分隔符为 - ，样式名为 new ，则 extend 参数为 -new 。

### 引用CSS和JS
只是引用的标签名不同：
```
{% qnjs jsFile attr1:value1 attr2:value2 'attr3:value31 value32 value3n' %}
{% qncss cssFile attr1:value1 attr2:value2 'attr3:value31 value32 value3n' %}
```

**注意**
在添加图片的时候，放在images文件夹下的图片，是可以创建子文件夹的。所以，为了方便以及同时兼容之前使用本地图片插件的方式，每一篇文章或者一个功能对应的图片最好放在一个子文件夹下，这样在日志文章中对图片进行引用的时候就可以直接设置成文件夹名/图片名.png的格式。这里建议文件夹名与文章名相同，便于管理


## 六、同步到七牛
---

将文件上传到七牛云有两种方式 `hexo s` 和 `hexo qiniu sync|sync2` 的两个命令工具:
```
qiniu sync plugin for hexo
Usage: hexo qiniu  <argument>
Description:
hexo qiniu static files sync plugin
Arguments:
  info | i    Displays plugin version, aurthor or GitHub links
  sync | s    Sync your static files to qiniu.
  sync2 | s2  Sync your static files to qiniu.(And uploaded update files)
```


## 七、后记
---

以下是自己搭建过程中查找的相关链接和信息，希望对大家有帮助。
1. https://github.com/gyk001/hexo-qiniu-sync
2. http://www.ixirong.com/2016/10/31/how-to-use-hexo-qiniu-sync-plugin/
3. http://yuchen-lea.github.io/2016-01-21-use-qiniu-store-file-for-hexo/
4. http://error408.com/2016/08/02/Hexo%E4%B8%83%E7%89%9B%E5%9B%BE%E5%BA%8A%E4%BD%BF%E7%94%A8/


[1]: https://www.qiniu.com/
[2]: https://zh.wikipedia.org/wiki/%E5%85%A7%E5%AE%B9%E5%82%B3%E9%81%9E%E7%B6%B2%E8%B7%AF
[3]: https://portal.qiniu.com/signup?code=3lg4nzgggsggi
[4]: https://portal.qiniu.com/create
[5]: https://github.com/gyk001/hexo-qiniu-sync
[6]: https://github.com/gyk001/hexo-qiniu-sync/issues/41#issuecomment-279229378
