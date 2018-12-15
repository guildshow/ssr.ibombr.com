---
layout: default
---

Ubuntu 16.04之前的版本中，因为是使用initd管理系统，我们可以直接在 `/etc/rc.local` 中设置开机启动程序。这里就不赘述了。

从16.04开始Ubuntu不再使用initd管理系统，改用systemd，因此其设置开机启动的方式相应的需要进行调整。

为了像以前一样，在 `/etc/rc.local` 中设置开机启动程序，需要以下几步：

## 1.链接文件

systemd默认读取 `/etc/systemd/system` 下的配置文件，该目录下的文件会链接 `/lib/systemd/system/` 下的文件。一般系统安装完之后，在 `/lib/systemd/system/` 路径下会有一个  `rc-local.service` 文件，即我们需要的配置文件。 我们把它链接过来：

```bash
ln -fs /lib/systemd/system/rc-local.service /etc/systemd/system/rc-local.service
```

## 2.编辑配置文件

以下：

```bash
cd /etc/systemd/system/
vi rc-local.service
```

而 `rc-local.service` 的内容编辑如下：

```bash
#  This file is part of systemd.
#
#  systemd is free software; you can redistribute it and/or modify it
#  under the terms of the GNU Lesser General Public License as published by
#  the Free Software Foundation; either version 2.1 of the License, or
#  (at your option) any later version.

# This unit gets pulled automatically into multi-user.target by
# systemd-rc-local-generator if /etc/rc.local is executable.
[Unit]
Description=/etc/rc.local Compatibility
ConditionFileIsExecutable=/etc/rc.local
After=network.target

[Service]
Type=forking
ExecStart=/etc/rc.local start
TimeoutSec=0
RemainAfterExit=yes
GuessMainPID=no

[Install]
WantedBy=multi-user.target
Alias=rc-local.service
```

## 3.创建和编辑 `rc.local` 文件

创建

```bash
touch /etc/rc.local
```

赋可执行权限

```bash
chmod 755 /etc/rc.local
```

编辑 `rc.local` ，添加需要开机启动的任务

```bash
#!/bin/bash	
/usr/bin/ssserver -c /etc/shadowsocks.json -d start
echo "ssserver start success" > /var/test_boot_up.log
```

## 4.验证

执行

```bash
reboot
```

重启系统验证OK。

最后，说一下

```bash
/etc/systemd/system/
```

下的配置文件 `(xxx.service)` , 其中有三个配置项，`[Unit]` / `[Service]` / `[Install]` 分别是：

* 1 [Unit] 区块：启动顺序与依赖关系。 
* 2 [Service] 区块：启动行为,如何启动，启动类型。 
* 3 [Install] 区块，定义如何安装这个配置文件，即怎样做到开机启动。

## 5.补充

在Ubuntu 16.04及以后版本安装ss服务时，相关的开机自启配置与之前文档：

* [使用VPS搭建SS服务器](index.md) 

中略有不同。除上述配置方式有修改之外，ssserver的安装路径也从 `/usr/local/bin/ssserver` 变成了 `/usr/bin/ssserver` 。因此rc.local才有以下调整：

```bash
#!/bin/bash	
/usr/bin/ssserver -c /etc/shadowsocks.json -d start
echo "ssserver start success" > /var/test_boot_up.log
```

## 6.参考文档

主要参考了以下文档：

* [ubuntu17.04使用systemd设置开机启动](https://blog.csdn.net/ydyang1126/article/details/77968768)

