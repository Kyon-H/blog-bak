---
layout: post
title: Foobar2000 安装
subtitle: Foobar2000 安装
date: 2024-12-24 20:51
author: Kyon-H
header-img: img/post-bg-2015.jpg
tags:
---
## Ubuntu安装Foobar2000

启动foobar2000需要显示器，无显示器可安装vncserver，并设置vncserver开机自启

#### 安装wine

#### 安装foobar2000

 [foobar2000_v1.6.11.exe](packages/foobar2000_v1.6.11.exe)

 [foobox_6.1.6.11.exe](packages/foobox_6.1.6.11.exe)

插件：

![image-20240823151625647.png](https://kyonk.v6.army:1443/CdWaUe.png)

#### 设置自启

创建启动脚本

```shell
vim /home/load/bin/foobar2000_start.sh
```

内容：

```sh
#!/bin/bash
sleep 60 #延时60秒
export DISPLAY=:1
xhost +
wine /home/load/Desktop/foobar2000/foobar2000.exe
```

设置权限

```shell
chmod +x /home/load/bin/start_foobar2000.sh
```

创建 systemd 服务文件

```shell
sudo vim /etc/systemd/system/foobar2000.service
```

内容：

```
[Unit]
Description=Foobar2000
After=network.target network.service vncserver_start.service

[Service]
Type=simple
User=load
KillMode=process
ExecStart=/home/load/bin/foobar2000_start.sh
Restart=always

[Install]
WantedBy=default.target
```

```shell
sudo systemctl enable foobar2000.service
sudo systemctl start foobar2000.service
```
