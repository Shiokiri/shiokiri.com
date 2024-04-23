---
title: Minecraft Spigot开服记录
tags: 游戏
---

<!--more-->

# 安装

系统是Debian 12

安装jre tmux

```shell
sudo apt install default-jre tmux
```

检查java环境

```
java --version
```

添加用户mc

```
useradd -m -s /bin/bash mc
```

切换用户mc，检查路径

```
su mc
cd ~
pwd
```

下载spigot最新版本BuildTools

```
mkdir spigot
cd spigot
wget https://hub.spigotmc.org/jenkins/job/BuildTools/lastSuccessfulBuild/artifact/target/BuildTools.jar
```

运行jar包，这里等待一会，必要时换一下Maven源

```
java -jar BuildTools.jar --rev 1.20.4
```

创建服务器目录

```
cd ~
mkdir mcserver
cp spigot/spigot-1.20.4.jar ./server/
cd server
```

先运行一次

```
java -Xmx1024M -Xms1024M -jar spigot-1.20.4.jar
```

查看eula.txt并同意协议（文件内容改为eula=true）

```
vim eula.txt
```

写一个脚本`start.sh`

```
#!/bin/sh
trap "exit" 2
java -Xmx1024M -Xms1024M -jar spigot-1.20.4.jar
```

新建一个tmux窗口

```
tmux new -s mc
```

运行脚本·

```
chmod +x start.sh
./start.sh
```

# 插件

## MOTD

[MOTD [1.8 - 1.20] \| SpigotMC - High Performance Minecraft](https://www.spigotmc.org/resources/motd-1-8-1-20.8390/)

用于实现motd消息显示

# 域名

DNS解析配置一条SRV记录即可隐藏端口

参考RFC2782`_Service._Proto.Name TTL Class SRV Priority Weight Port Target`

```
_Service._Proto.Name 设为 _minecraft._tcp.mc.example.com
TTL 设为 30min
Priority Weight Port Target 设为 5 0 25565 example.com
```

