---
layout: post
title: Crawl-Everything
category: Web
description: 爬虫的简单备忘
---

稍微复杂的备忘一下写过的爬虫。目前抓取过的网页大概有如下：  
* 百度百科  
* 知乎  
* 微博  
* loft,tumblr   
* 某些定时查询脚本

## Introduction  
![crawler-arch][crawler-arch]  
如wiki里面的图所示，爬虫是一个下载工具，把需要爬的url加入到队列中，然后有需要的线程去队列中把需要的爬取的页面中的元数据存储到本地。最开始需要用到爬虫的项目是搜索引擎，每遇到一个网页，就把这个网页所有满足要求的链接都加入到队列中去，然后子线程去消费队列，把url对应的文本和其他元数据存到本地，是一个树状分裂的爬取过程。市面上大部分的爬虫框架都是以这个原理工作的，这样的框架**多适用于爬取整站的网站数据或者可以明确遍历到全站链接的网站**。


## [百度百科][baike-bd]
百科都是一些静态网页，不会因为是否用户登录或者不同用户而改变内容，通常网页以html,htm或者txt等结尾，此外，这种网页的语料有一定的规则，例如 http://baike.baidu.com/view/\*.html ， 只要把星号替换成某个数字，就可以得到某个词条的页面。这样一来，只要从1开始递增向上遍历即可。
简单描述一下算法：  

```shell
~~~shell
index=1
end=1000000000
template="http://baidu.com/view/"
fin='.htm'
while [ $index -lt $end ]
do
        url=$template$index$fin
        curl $url
        index=`expr $index + 1`
done
~~~shell
```

可以用一个简单的脚本完成任务，但是一般来说组织不这样用，通常会用大的框架，比如说scrapy来完成，包含容错机制和重试机制等。

## [知乎][zhihu]  

知乎和百科比起来差别比较大，体现在知乎的内容需要登陆才能完整看见，同时知乎的内容并不是静态网页，所以不能直接按照上述的方案做。为了解决登陆问题，可以使用cookie伪装成已经登陆的状态进行发送http请求。以爬取一个问题的答案(https://www.zhihu.com/question/31496671) 为例子说明。  
![zhihu-header][zhihu-header]  
通过chrome的控制台,通过目测（一般在xhr里面或者看每个请求的response）可以发现请求答案的http主要是这个，可以在控制台页面中发现request header中有一个**cookie**项，有了这个选项就可以绕开用户登录的问题了。可以使用你在页面登录中所获得的cookie项填充到代码里，伪装成一个已经登陆好的用户发送http请求即可。  

```
~~~shell
curl -c cookie_filename -H header https://www.zhihu.com/node/QuerstionV2 -d '{"method":"next","params":{...},"_xsrf":"..."}'
#使用curl 模拟请求，-c用来控制cookie，-H用来添加其他header，-d用来post data

#python中url post请求的写法
req = urllib2.Request(url,data)
req.add_header('Content-Type', 'application/json')
req.add_header('cookie', '....')
try:
        res = urllib2.urlopen(req)
        data = res.read()
        print data
except urllib2.HTTPError as e:
        print text
        print e.code
        print e.read()
~~~
```  

## weibo
微博的数据主要两种获取方式，一种是通过[开放平台](http://open.weibo.com/wiki/%E5%BE%AE%E5%8D%9AAPI)提供的接口获取，这个方法首先你得注册一个应用，然后有某个账号授予你token，根据不同接口可以获得一部分的数据，使用官方提供的sdk进行二次开发，由于不用自己解析数据，相对方便，缺点是接口有调用频率，不能像自己原先发送http请求一样有更高的频率；另外一种就是自己动手发http请求获取资源；还有一种就是走wap端口（并没有试过，听别人介绍的）。常见的微博数据爬取主要是三种，一种是特定主题的微博（例如药家鑫杀人案），另一种是某批用户的全部微博（主要是历史数据），最后一种是自己关注的人的时间线（从某个时间段开始的全部数据）。  

#### 特定主题的微博  
根据debug出来的http请求列表，发现相对好拿，每一页都是静态的，数据不是通过ajax的方式获得。  
![weibo-header][weibo-header]
发送一个带cookie的http请求就可以了。如果有第二页，就在http的url上面加一个page=2的参数即可。

#### 特定用户的微博
根据uid获取微博，其中每页包括一次GET请求和两次ajax请求，第一个请求和前面的请求类似，后面的两个请求类似于这样：  
![weibo-ajax][weibo-ajax]  
不难发现红框里面的参数比较多，而且每次都会发生改变，并不是特别容易明白具体的含义，需要花点时间去寻找其中的规律。多拿几个类似请求比对一下，弄明白之后就可以发http请求资源了。

#### 自己的时间线
有的时候自己想定制一波数据，现成的都不好用，或者需要比较新的数据，那就自己建一个账号，然后关注想要获取数据的人，不断的刷新自己的timeline就可以拿到想要的数据了。debug一下可以发现timeline也是一次主动请求和两次ajax请求，弄清楚参数即可。

## [loft][loft],tumblr
之所以单列提出loft和tumblr出来，是因为用了一种别的策略，有感于以上的数据的抓取都要花很多时间去研究http请求的参数，费时费力就尝试了一种别的方法，利用前端的测试工具selenium，模拟一个浏览器的存在，用代码实现表单的提交，按钮的点击等，由于这个测试工具包含了浏览器的内核，会自行加载所有的ajax，js，css等请求，每次都只要解析浏览器中网页的源码就可以了。之所以前面的weibo,zhihu没采用这种方法，主要是因为有登陆需要验证码的问题，就目前的OCR水平还不足以完全实现自动输入验证码，所以还是用http+cookie比较可行，当然也可以手动登陆一次，然后用selenium+cookie的方式进行。  
稍微讲解一下模拟登陆的问题。如图是loft登陆的界面，可以看到没有验证码。  
![loft-login][loft-login]
按照我们通常的逻辑，就是点击email的输入框，输入email，然后点击密码框，输入密码，最后点击登陆按钮。下面我们翻译成python代码  

```Python
~~~python
from selenium import webdriver

driver = webdriver.Firefox(timeout=60)
driver.get("http://www.lofter.com/login?urschecked=true"
# 输入email
email = driver.find_element_by_id("email")
assert email != None and email.is_displayed()
email.clear()
email.send_keys(email_text)

# 输入密码
password = driver.find_element_by_id("password")
assert password != None and password.is_displayed()
password.clear()
password.send_keys(pass_text)

# 点击登陆按钮
butt = driver.find_element_by_id("login_button")
assert butt != None and butt.is_displayed()
print 'ok'
butt.click()

time.sleep(10) #通常休眠一段时间，等待页面完成加载或者跳转

print driver.page_source.encode('utf-8') # 打印页面源码

# 通常我们为了触发ajax请求，会让页面下拉，在这里反映为
driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")
~~~
```

由于tumblr时而在国内，时而在国外，最好通过代理服务器进行连接。本文这里使用了ss代理，下面做简单备忘关于配置代理。关于ss代理看[这里](http://cdmaok.github.io/web/2016/04/08/Break-the-Wall/)  

```Python
~~~python
def setup_proxy(PROXY_HOST,PROXY_PORT):
        fp = webdriver.FirefoxProfile()
        fp.set_preference("network.proxy.type", 1)
        fp.set_preference("network.proxy.socks_remote_dns", True)
        fp.set_preference("network.proxy.socks", PROXY_HOST)
        fp.set_preference("network.proxy.socks_port", int(PROXY_PORT))
        fp.update_preferences()
        return webdriver.Firefox(firefox_profile=fp,timeout=60)
~~~
```



## Summary
1. 常用的爬虫策略： 多账户，多代理，模拟用户进行写请求。
2. 如何选择合适的爬虫，debug一下： 如果是静态网页，那么直接http请求或者scrapy；如果是走的restful-style的api请求，尽量还是直接发http请求；如果需要登录，没有验证码，那么首选selenium或者其他测试框架；如果需要登录而且需要验证码，考虑使用http+cookie或者selenium+cookie的方式。  

## 工具列表
1. linux命令**curl**，用来发送和接受http请求
2. **chrome**，或者其他带调试界面的浏览器
3. **scrapy**，python开源爬虫框架
4. 插件**postman**,和curl类似，不过有图形界面，可以用鼠标操作
5. **urllib，urllib2**，python普通的http类库
6. **selenium**,一个前端测试框架

## reference
1. http://weibo.com/ttarticle/p/show?id=2309403979725117724876

[baike-bd]:http://baike.baidu.com/  
[zhihu]:http://www.baidu.com/
[loft]:http://www.lofter.com/login?urschecked=true
[crawler-arch]:http://7xpv97.com1.z0.glb.clouddn.com/WebCrawlerArchitecture.png
[zhihu-header]:http://7xpv97.com1.z0.glb.clouddn.com/zhihu_header.jpg
[weibo-header]:http://7xpv97.com1.z0.glb.clouddn.com/weibo_header.jpg
[weibo-ajax]:http://7xpv97.com1.z0.glb.clouddn.com/weibo_ajax.jpg
[loft-login]:http://7xpv97.com1.z0.glb.clouddn.com/loft_login.jpg
