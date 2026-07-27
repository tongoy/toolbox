# Microsoft Office 2024安装与激活教程


## 下载与安装Microsoft Office

默认使用微软官方提供的[Office Deployment Tool (ODT)](https://www.microsoft.com/en-us/download/details.aspx?id=49117)工具。除此之外，还有[第三方工具](https://www.coolhub.top/archives/42)可以使用。
该工具的使用可以参考[官网文档](https://learn.microsoft.com/en-us/office/ltsc/2024/deploy)。使用[Office Customization Tool (OCT)](https://config.office.com/deploymentsettings)工具生成配置文件后便可以下载和安装。我选择的是`Office LTSC Professional Plus 2024 - Volume License`版本。
注意：KMS激活方式无法激活零售（Retail）许可证，只能激活批量（Volume）许可证。激活有效期为180天，过期后再次激活即可。


## 激活Microsoft Office

### 构建KMS服务器

#### 安装KMS服务器程序
从Windows 8.1开始，KMS服务器不能是要激活的机器本身。在Ubuntu主机上，执行以下命令安装KMS服务器程序[vlmcsd](https://github.com/Wind4/vlmcsd)：
```
apt-get install gcc git make -y
mkdir /usr/local/kms
cd /usr/local/kms
git clone https://github.com/Wind4/vlmcsd.git
cd vlmcsd
make

netstat -tuln # make sure TCP port 1688 is available before running vlmcsd
```

启动vlmcsd程序：
```
/usr/local/kms/vlmcsd/bin/vlmcsd -L 0.0.0.0:1688 -l vlmcsd.log
```

关闭vlmcsd程序：
```
ps aux | grep vlmcsd | grep -v grep # get vlmcsd pid
kill pid
```

或者使用[一键安装脚本](https://github.com/dakkidaze/one-key-kms)。

#### 验证KMS服务器程序
验证KMS服务器正常工作：
```
/usr/local/kms/vlmcsd/bin/vlmcs
```

出现类似如下内容即可：
```
Connecting to 127.0.0.1:1688 ... successful
Sending activation request (KMS V6) 1 of 1  -> 03612-00206-563-807919-03-1054-14393.0000-1162024 (3A1C049600B60076)
root@vultr-ss:/usr/local/kms/vlmcsd/bin#
```

注意：`vlmcsd`默认使用TCP端口1688。如果启用了防火墙，请确保端口1688已打开，以允许KMS主机服务通过。

如果无法构建自己的KMS服务器，可以在[这里](https://www.coolhub.top/tech-articles/kms_list.html)找到可用的KMS服务器。

### 激活客户机

#### Microsoft Windows OS
```
slmgr /skms 192.168.1.17:1688 # change 192.168.1.17 to the hostname or IP address of your KMS server
slmgr /ato

slmgr /dlv
slmgr /xpr
```

#### Microsoft Office 2024
```
# To use ospp.vbs you'll have to change the current directory to your Office installation.
cd C:\Program Files\Microsoft Office\Office16

cscript ospp.vbs /sethst:192.168.1.17 # change 192.168.1.17 to the hostname or IP address of your KMS server
cscript ospp.vbs /setprt:1688
cscript ospp.vbs /act

cscript ospp.vbs /dstatus
```

出现类似如下内容即为激活成功：
```
<Product activation successful>
```