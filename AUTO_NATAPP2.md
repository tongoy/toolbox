# 在外网中访问内网的服务器

这里使用一种内网穿透方法，可以轻松地在任何地方访问某个局域网中的服务器。



## 1. 购买与配置NATAPP

首先购买[内网穿透工具NATAPP](https://natapp.cn/)和[数据流量](https://natapp.cn/flow/lists)，按照[网站](https://natapp.cn/article/natapp_newbie)[教程](https://natapp.cn/article/tcp)配置好并连接成功。



## 2. 定时运行NATAPP

在`/data1`目录下创建脚本文件`conn_in_tmux.sh`，编写以下代码：
```
tmux kill-session -t natapp2 # if tmux does not have this session, the following commands will still be executed
tmux new-session -s natapp2 -d
tmux send-keys -t natapp2 'date >> /data1/conn.log' C-m
tmux send-keys -t natapp2 'conda activate fewshot' C-m
tmux send-keys -t natapp2 'python3 /data/docker_1122_8_env/client2.py -u 3120185492 -p OUYANGT******* -a login >> /data1/conn.log' C-m # connect server to the Internet
sleep 30s
tmux send-keys -t natapp2 'conda deactivate' C-m
#tmux send-keys -t natapp2 'ls' C-m
tmux send-keys -t natapp2 '/data1/natapp -authtoken=f9fb7ff73fc3c471' C-m
```

增加可执行权限`chmod +x conn_in_tmux.sh`，运行该脚本`/data1/conn_in_tmux.sh`可以在服务器后台创建一个tmux会话并运行该内网穿透工具。


编辑crontab，`crontab -e`，在最后一行添加代码`0 9 * * * /data1/conn_in_tmux.sh`以定时执行上述脚本文件`conn_in_tmux.sh`。保存后重启crontab服务`service cron restart`。
需要注意的是，如果docker中不存在名称为`natapp2`的tmux会话（此时不管docker中有没有别的tmux会话），`tmux kill-session`命令都不会中断脚本的执行。



## 3. 开机运行NATAPP

到此为止，你的docker系统已经开始在UTC早上9点定时执行`conn_in_tmux.sh`脚本了。
但是，我们还需要保证服务器或docker重启后，`conn_in_tmux.sh`脚本仍能定时执行。退出docker系统并执行`docker restart tong_1122_8`命令后再次进入docker系统`docker exec -it tong_1122_8 bash`，此时执行`ps -ef | grep cron`命令或`service cron status`命令可以看到crontab服务并未开启。
此时最简单的解决方法就是使用`systemctl enable cron.service`命令让crontab服务开机自动启动。但在docker中`systemctl`命令使用受限，导致该命令并不能正确地被执行。此时参考[这篇博客](https://blog.csdn.net/qq_38603541/article/details/124028994)可以解决crontab服务开机自动启动的问题。

在`/home`目录下创建脚本文件`start_cron.sh`，编写以下代码：
```
date >> /home/start_cron.log
service cron start >> /home/start_cron.log
```

增加可执行权限`chmod +x start_cron.sh`后，打开`/root/.bashrc`文件并在末尾添加以下代码：
```
# start cron
if [ -f /home/start_cron.sh ]; then
    . /home/start_cron.sh
fi
```

此时，在执行`docker restart tong_1122_8`命令和`docker exec -it tong_1122_8 bash`命令后，`/root/.bashrc`文件会被docker系统分别执行一次。此时执行`ps -ef | grep cron`命令或`service cron status`命令可以看到crontab服务已经在运行中。
由于docker随服务器开机自动启动，因此服务器重启后依然可以使用内网穿透工具在外网中连接到服务器，只是需要等到UTC早上9点定时执行`conn_in_tmux.sh`脚本后。


