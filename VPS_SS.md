# 在Windows中使用VPS和SS



## 1.购买VPS服务器

VPS的全称是Virtual Private Server，即虚拟专用服务器。
我使用的VPS供应商是[Vultr](https://www.vultr.com/)，一个月5美元左右。



## 2.配置VPS服务器

首先往Vultr账户里充钱，然后选择适合自己的服务器，记得勾选上`Enable IPv6`这个选项。

服务器发布成功后，在`Overview`页面中可以查看服务器root账号的密码，在`Settings`页面中可以查看服务器的IPv4/IPv6地址。

使用Vultr自带的命令行终端或使用本地的任何一种远程连接工具（例如，Xshell），使用root账号登录服务器。

依次执行以下命令：
```
# 更新软件源，安装软件更新
apt-get update
apt-get upgrade

# 安装服务器端SS
wget --no-check-certificate -O shadowsocks-all.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh

chmod +x shadowsocks-all.sh

./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log

```

接下来选择`2) ShadowsocksR`，然后输入密码，剩下的按回车键保持默认选项。等待一会儿后显示出服务器端的配置结果，安装完毕。



## 3.配置本地SS

Windows Shadowsocks客户端
https://github.com/shadowsocks/shadowsocks-windows/releases

Windows ShadowsocksR客户端
https://github.com/shadowsocksrr/shadowsocksr-csharp/releases

MacOS Shadowsocks客户端
https://github.com/shadowsocks/shadowsocks-iOS/releases

MacOS Shadowsocks客户端
https://github.com/shadowsocks/ShadowsocksX-NG/releases

以Windows Shadowsocks客户端为例。
打开配置界面，按照第二步中显示的服务器配置结果填写服务器地址（IPv4或IPv6）、服务器端口、服务器密码、加密、代理端口默认为1080，点击确定。

右键点击小飞机图标，选择启用系统代理，系统代理模式选择全局模式（国内国外所有网站都使用代理）或PAC模式（仅仅一些国外网站使用代理）。然后便可以访问谷歌等国外网站了。

> 通常而言并不需要启用系统代理，而是通过本地联网软件提供的代理功能和SS客户端提供的代理端口来使用VPS代理服务器上网。

在Chrome浏览器中，提供代理功能的插件一般选择Proxy SwitchyOmega。



## 4.测试

访问[Google](https://www.google.com/)。



## 5.卸载

若已安装多个版本，则卸载时也需多次运行（每次卸载一种）。使用root账号登录，运行以下命令：
```
./shadowsocks-all.sh uninstall
```



## 6.启动，停止，重启，查看状态

不同版本的启动，停止，重启，查看状态的命令如下：
```
# Shadowsocks-Python
/etc/init.d/shadowsocks-python start|stop|restart|status

# ShadowsocksR
/etc/init.d/shadowsocks-r start|stop|restart|status

# Shadowsocks-Go
/etc/init.d/shadowsocks-go start|stop|restart|status

# Shadowsocks-libev
/etc/init.d/shadowsocks-libev start|stop|restart|status
```

不同版本的默认配置文件如下：
```
# Shadowsocks-Python
/etc/shadowsocks-python/config.json

# ShadowsocksR
/etc/shadowsocks-r/config.json

# Shadowsocks-Go
/etc/shadowsocks-go/config.json

# Shadowsocks-libev
/etc/shadowsocks-libev/config.json
```



## 7.配置Chrome Proxy SwitchyOmega

Proxy SwitchyOmega插件可以从Chrome商店中[下载](https://chrome.google.com/webstore/detail/proxy-switchyomega/padekgcemlokbadohgkifijomclgjgif)。

点击左侧情景模式中的新建情景模式，情景模式名称为`vps-ss`，情景模式类型选择代理服务器，点击创建。

在代理服务器中，代理协议选择`SOCKS5`，代理服务器为`127.0.0.1`，代理端口为`1080`。

点击左侧的应用选项。


