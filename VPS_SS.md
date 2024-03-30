# 在Windows中使用VPS和SS



## 1.租用VPS服务器

VPS的全称是Virtual Private Server，即虚拟专用服务器。我使用的VPS供应商是[Vultr](https://www.vultr.com/)，一个月5美元。

使用VPS的好处是，虽然价格和购买现成的VPN差不多，但是租用VPS就拥有了海外的一台主机（可以搭建自己的博客等等）。



## 2.配置VPS服务器

首先往Vultr账户里充钱，然后选择适合自己的服务器，记得勾选上`Enable IPv6`这个选项。

注意，服务器的操作系统不能选择过高的版本，例如`Ubuntu 22.04 LTS x64`操作系统会仅默认安装`Python 3.10.12`，但[Shadowsocks服务器端代码](https://github.com/shadowsocks/shadowsocks/tree/master)是基于Python2+编写的，因此即使将`shadowsocks-all.sh`文件中的`python`全部替换成`python3`以避免出现`[Error] Failed to install python`错误，安装结束后还是会出现`Starting Shadowsocks failed`或`Starting ShadowsocksR failed`等错误。
为了避免额外的错误，我选择的是`Ubuntu 20.04 LTS x64`操作系统，该系统中python和python2使用的是`Python 2.7.18`，python3使用的是`Python 3.8.10`。

服务器发布成功后，在`Overview`页面中可以查看服务器root账号的密码，在`Settings`页面中可以查看服务器的IPv4/IPv6地址。

### 2.1 安装
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

### 2.2 启动，停止，重启，查看状态
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

### 2.3 卸载
若已安装多个版本，则卸载时也需多次运行（每次卸载一种）。使用root账号登录，运行以下命令：
```
./shadowsocks-all.sh uninstall
```

更多详细的安装信息可以查看`shadowsocks_install`项目的[开源代码](https://github.com/teddysun/shadowsocks_install)和[官方博客](https://teddysun.com/486.html)。



## 3.配置本地SS

下载[Windows Shadowsocks客户端](https://github.com/shadowsocks/shadowsocks-windows/releases)，我使用的是`Shadowsocks-4.1.3.1`版本，不用安装解压即可。填写完服务器地址、服务器端口、密码和加密之后（代理端口默认为1080），点击确定即可。

右键点击小飞机图标，选择启用系统代理，系统代理模式选择全局模式（国内国外所有网站都使用代理）或PAC模式（仅仅一些国外网站使用代理），然后测试一下能否打开[Google页面](https://www.google.com/)。
如果无法打开，右键点击小飞机图标，然后在帮助里选择显示日志。
如果日志中显示的是`time out`错误，可以使用在线工具进行诊断。例如使用[ping](https://ping.pe/)工具来判断服务器IP是否被大陆封禁、使用[端口扫描](https://tool.chinaz.com/port)工具来判断服务器上的代理端口是否开启/可以访问。
对于第一种情况，如果IP地址被封禁，就只能更换一台服务器。在vultr中更换服务器不会额外支付费用，更换新的服务器时最好更换到其他的地区，不然IPv4地址可能不会更新。
对于第二种情况，如果端口是关闭状态，可以先登录服务器使用`ss -tunlp`或`netstat -tuln`命令查看本地代理端口是否打开，如果已经打开，那么使用`ufw status`命令查看防火墙状态，如果是`active`状态，使用`ufw disable`命令关闭防火墙即可（使用`ufw enable`命令可以再次开启防火墙）。

如果遇到其他的问题可以在github相应客户端的[issues](https://github.com/shadowsocks/shadowsocks-windows/issues)上找到答案。

[MacOS Shadowsocks客户端1](https://github.com/shadowsocks/shadowsocks-iOS/releases)和[客户端2](https://github.com/shadowsocks/ShadowsocksX-NG/releases)同理。

> 通常而言并不需要启用系统代理，而是通过本地联网软件提供的代理功能和SS客户端提供的代理端口来使用VPS代理服务器上网。

在Chrome浏览器中，提供代理功能的插件一般选择Proxy SwitchyOmega。



## 4.配置Chrome Proxy SwitchyOmega

Proxy SwitchyOmega插件可以从Chrome商店中[下载](https://chrome.google.com/webstore/detail/proxy-switchyomega/padekgcemlokbadohgkifijomclgjgif)。

点击左侧情景模式中的新建情景模式，情景模式名称为`vps-ss`，情景模式类型选择代理服务器，点击创建。

在`vps-ss`情景模式下，代理服务器的代理协议选择`SOCKS5`，代理服务器为`127.0.0.1`，代理端口为`1080`。不代理的地址列表保持默认。

最后点击左侧的应用选项即可。


