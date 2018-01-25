---
title: 在微信、微博、QQ、Safari唤起App的解决方案
date: 2018-01-25 18:11:47
tags: 移动端
---
最近在做微信、QQ、微博中使用js唤起App，之前也做过类似的功能，不过比较粗糙，考虑的情况不太全，而且那已经是很久之前的事情了，很多技术都已过时，现在有体验更好，功能更加完善的唤起技术，之前的很多的方案，到了现在都已是不太必要了，现在通过这篇文章分享给大家一个全面的、最新的唤起方案，希望对大家有帮忙。

**目标**

<!-- more -->

用户点击打开App或者下载按钮（这个按钮可能在一个下载入口页、各种分享页面的吸顶或吸底的banner），如果用户已经安装了App，则根据业务跳转到相应的Native页面；如果用户没有安装该应用，则跳到AppStore或者应用市场去下载我们的App。

**一个完整的流程**


首先所有的下载/唤起入口都是一个直接跳转，应该是这样：

<ahref="https://applink-party.mtime.cn/mtlf">下载</a>
或者这样：

window.location.href = 'https://applink-party.mtime.cn/mtlf'
所有的业务判断都是mtlf这个页面里面来做，这样有两个好处：

1. 多业务共用代码。在一个团队中，每个人的业务都可能有一个banner下载，没有比location到一个url更简单的调用方式了
2. 能够利用universal link

**简单说下universal link**

在iOS9之前，唤起方式和现在安卓是一个的，都是使用scheme进行唤起，这种方式有个小问题，每次唤起，都会给个提示：是否打开xx应用，这样从体验上来讲，又让用户多一步操作。universal link会直接跳转，不会在页面做停留，条件就是在我们项目的根目录，增一个apple-app-site-association.json文件，里面的内容大致是这样：

![](https://i.imgur.com/UYAcpX7.png)

然后iOS的App后台再配置一下，就可以实现直接唤起了！

**微信、微博、QQ、Safari在各平台的唤起方案**

![](https://i.imgur.com/K8soWRi.png)

经过长时间的实验，总结了这张在各种情况下，唤起成功/唤起失败的解决方案，我们接下来一个一个的说。

**微信**

微信是最重要的一种分享渠道，但是我们能够做的，却不多。之前，iOS下的微信支持universal link这种唤起方式，但是从2018年1月8日之后，**微信把这个给屏蔽了！！！**不管微信基于什么原因，把iOS下这种最便捷的唤起方式屏蔽，我们能做的只能是适应了。so，现在不管是iOS还是android，我们的处理方式是一样的：**都是直接跳到应用宝**。iOS的应用宝会引导找开AppStore，android的应用宝会直接打开App（前提是你已经下载）
注：微信把itunes链接也屏蔽了，所以也没办法直接跳转AppStore，只能借助应用宝来搭这个桥。

**微博**

微博目前还支持universal link唤起，我们只需要考虑未下载的情况。



- 在iOS下，微博是不支持打开应用宝的链接，所以我们需要引导用户使用Safari打开，像这样：

![](https://i.imgur.com/YuVx2NT.png)

- 在android平台下，使用scheme这种方式是唤不起App的，但是有特例，同样是scheme，大人点评和网易云音乐就可以唤起，有空大家可以自己试试，所以我们可以推断出，安卓平台下的微博，也有类似微信一样的白名单，在白名单内的，就可以使用scheme唤起，就像微信之于京东，京东在微信里面就是通过scheme方式唤起的。

so，不管是iOS还是android，我们的方案是：直接引导用户使用本地浏览器打开。

**QQ**

- iOS平台下，QQ目前还支持universal link唤起，要是没有安装，QQ下也支持直接打开itunes链接，比较其他应用，QQ支持是最好的。
- android平台下，QQ也支持scheme方式唤起，但是在一些老机型下，QQ会有一定的概率唤起失败，具体的现象是：第一次打开页面，唤起失败，再次打开，唤起成功。根据现象，我们可以推测出，在QQ的webview中，会对scheme的唤起方式做一些加载时间上的限制，经测试，大约在500ms，超过这个时间值，就会出现唤起失败的情况。为什么第二次打开，唤起成功的概率会大，是因为第一次加载时，已缓存了文件，第二次打开直接加载，这样时间在限制之内。

**Safari**

Safari这种情况比较简单，支持universal link，也支持直接打开itunes，so，如图处理就可以了。

**踩坑**

H2M_LI_HEADER

- 在iOS9中，Safari不支持直接跳转itunes，so，这种情况需要做兼容处理，可以直接跳到应用宝
- 之前看唤起是不是成功了，需要自己来计算时间，因为要是唤起成功了，setInterval的时间就会变慢，经我测试，已经用不到这种方法了，只需要使用document.hidden || document.webkitHidden就可以，兼容性还不错
- 判断是不是Safari浏览器时，一般判断都是UA中有没有这个字符串，经测试发现，安卓的UA中，也包含Safari这个字符串（如下UA展示），所以需要加上操作系统的判断

- 关于Scheme唤起，之前有很多方案，
- 比如：使用iframe、<a>标签点击、window.location…经测试，只要使用a标签点击，这样兼容性最好，代码大约长这样：

![](https://i.imgur.com/RXwBI0p.png)


**关于测试**

两个平台，这么多情况，要一个一个测试吗？当然要一个一个的验证，但是在开发期间，没有必要改一行，在手机上测试一下，这样效率太低了，尤其是像一样，选了一个安卓4.4的手机，绝对可以磨练你的耐心。为了提高效率，我把我常用到的UA分享给大家，这样在Chrome模拟器里配置一下，就可以本地调试了，常用UA如下：

- iOS-微信

Mozilla/5.0 (iPhone; CPU iPhone OS 11_2_2 like Mac OS X) AppleWebKit/604.4.7 (KHTML, like Gecko) Mobile/15C202 MicroMessenger/6.6.1 NetType/WIFI Language/zh_CN

- iOS-QQ

Mozilla/5.0 (iPhone; CPU iPhone OS 11_2_2 like Mac OS X) AppleWebKit/604.4.7 (KHTML, like Gecko) Mobile/15C202 QQ/7.3.5.473 V1_IPH_SQ_7.3.5_1_APP_A Pixel/1125 Core/UIWebView Device/Apple(iPhone X) NetType/WIFI QBWebViewType/1

- iOS-微博

Mozilla/5.0 (iPhone; CPU iPhone OS 11_2_2 like Mac OS X) AppleWebKit/604.4.7 (KHTML, like Gecko) Mobile/15C202 Weibo (iPhone10,3__weibo__8.1.0__iphone__os11.2.2)

- iOS-safari

Mozilla/5.0 (iPhone; CPU iPhone OS 11_2_2 like Mac OS X) AppleWebKit/604.4.7 (KHTML, like Gecko) Version/11.0 Mobile/15C202 Safari/604.1

- android-微信

Mozilla/5.0 (Linux; Android 4.4.2; PE-TL20 Build/HuaweiPE-TL20; wv) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/57.0.2987.132 MQQBrowser/6.2 TBS/043807 Mobile Safari/537.36 MicroMessenger/6.6.1.1220(0x26060135) NetType/WIFI Language/zh_CN

- android-QQ

Mozilla/5.0 (Linux; Android 4.4.2; PE-TL20 Build/HuaweiPE-TL20; wv) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/57.0.2987.132 MQQBrowser/6.2 TBS/043807 Mobile Safari/537.36 V1_AND_SQ_7.3.2_762_YYB_D QQ/7.3.2.3350 NetType/WIFI WebP/0.3.0 Pixel/1080

- android-微博

Mozilla/5.0 (Linux; Android 4.4.2; PE-TL20 Build/HuaweiPE-TL20) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/30.0.0.0 Mobile Safari/537.36 Weibo (HUAWEI-PE-TL20__weibo__8.0.2__android__android4.4.2)

配置完成之后，就可以像我一样，在电脑上切换环境啦：


源代码以及库文件使用，请参见：https://github.com/sunhaikuo/js-arouse-app