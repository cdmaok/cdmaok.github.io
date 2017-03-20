---
layout: post
title: Break the Wall
category: Web
---

简单备忘一下

### requirements

1. Shadowsocks及其账号一枚
2. 浏览器插件（chrome下的swithyOmega 或者 搜狗浏览器下的swithySharp 或者foxfire里面的其他代理插件）

### steps

配置环境：win 7 PC，chrome浏览器
1.  打开Shadowsocks，配置,然后添加服务器账号的各种配置，（端口为1080）。  
![Shadowsocks_config][Shadowsocks_config]
2. 安装swithyOmega插件，然后配置一下。

  * 新建 **代理服务器模式**，命名为proxy，配置如图。
  ![proxy_setting][proxy_setting]  
  * 新建 **自动切换模式**，命名为penny,然后添加一定的域名规则，如图所示。
  ![domain_setting][domain_setting]  

  * 应用配置，如图所示。![chrome_setting][chrome_setting]

It should work.

### For other softwares

以上只是为了浏览器可以突破天际，为了发挥全部功能，让其他软件都可以无限制，就要设置代理服务器。  
服务器： **127.0.0.1:1080**  
比如eclipse,npm,maven等都可以修改配置文件。  
再来就是令自己的code甚至爬虫可以走ss。![python_proxy][python_proxy]  

图床配置起来太麻烦。简单用文字好了。

[Shadowsocks_config]:http://7xpv97.com1.z0.glb.clouddn.com/break_the_wall_01.jpg
[proxy_setting]:http://7xpv97.com1.z0.glb.clouddn.com/proxy_settings.png
[domain_setting]:http://7xpv97.com1.z0.glb.clouddn.com/domain_settings.png
[chrome_setting]:http://7xpv97.com1.z0.glb.clouddn.com/chrome_settings.png
[python_proxy]:http://7xpv97.com1.z0.glb.clouddn.com/python_proxy.png
