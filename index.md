---
layout: default
---

最近尝试着使用VPS自行搭建SS服务器（shadowsocks），自行解决科学上网方案。结果发现，有如下好处：

- 设置过程并没有想象中那样复杂；
- 科学上网之后，速度极快；
- 费用非常便宜；

以下说一下过程。

## PART1：依赖工具

主要有4个东西：

- VPS平台：[Digital Ocean](https://m.do.co/c/b6ee2fbd15a1)或[Vultr](http://www.vultr.com/?ref=6952194-3B) ；需注册帐号；
- SS服务器软件 : 可用SSH命令在服务端自行下载（后详）；
- SS客户端软件： 请自行百度ShadowSocks或ShadowSocksR下载；
- SSH客户端软件：**PUTTY**；[点击下载](https://the.earth.li/~sgtatham/putty/latest/x86/putty.exe)；

其中[Digital Ocean](https://m.do.co/c/b6ee2fbd15a1)或[Vultr](http://www.vultr.com/?ref=6952194-3B) 的注册流程，可参考以下文章：

> [百度经验： DigitalOcean免费VPS申请试用教程](http://jingyan.baidu.com/article/29697b91c09710ab20de3c86.html)；

> [百度经验：Vultr便宜VPS主机申请教程](http://jingyan.baidu.com/article/cbf0e500f8954f2eaa28932b.html)；

## PART2：VPS创建

拥有[Digital Ocean](https://m.do.co/c/b6ee2fbd15a1)或[Vultr](http://www.vultr.com/?ref=6952194-3B) 的帐号以后，我们来搭建一个VPS服务器；

教程如下（以下仅以Digital Ocean为例说明，Vultr大同小异）：

> [使用Digital Ocean快速创建云主机](https://www.xiaoz.me/archives/4678?replytocom=2335)；

此时，我们就拥有一个VPS主机了。

然后你会收到一封邮件，说明以下信息：

- 该VPS服务器的固定IP（IP Address）；
- 该VPS服务器的用户名（Username），目前总是root；
- 该VPS服务器的密码，（Password）；

如下图所示：

![email_ssh](http://upload-images.jianshu.io/upload_images/1018963-bbd5f6153b3a2b3a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后，我们就可以进行下一步了。

## PART3：连接服务器

使用PUTTY连接服务器；如下图所示位置输入服务器的固定IP：

![配置putty](http://upload-images.jianshu.io/upload_images/1018963-cd3613401c855498.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后点击OPEN，使用用户名Username `root` 和对应密码Password来登录，成功后如下图：

![连接](http://upload-images.jianshu.io/upload_images/1018963-00b4c6c413fd07be.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

连接成功后，进入下一步；

## PART4：配置SS服务器

根据系统情况，如果是Ubuntu，默认用

```bash
apt-get
```

命令来更新和安装应用。如果是centOS，则默认用

```bash
yum
```

来安装和更新应用。

### 在Ubuntu下安装

在连接窗口，依次输入以下命令：

![ssh控制台](http://upload-images.jianshu.io/upload_images/1018963-bd0ad3babffd78a8.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**更新**：

```bash
apt-get update
```

**安装**：

```bash
apt-get install python-pip
pip install setuptools
pip install shadowsocks
```

这样，Shadowsocks的服务器就搭建好了。

### 在CentOS下安装

在连接窗口，依次输入以下命令：

**更新**：

```bash
yum update
```

**安装**：

```bash
yum install python-pip
pip install --upgrade pip
pip install shadowsocks
```

这样，Shadowsocks的服务器就搭建好了。

如果安装时提示：

![nopythonpip](F:/Work@Home/Git@Home/Coding/01.iWanr_docs/Docsify_DocumentText/docs/_images/ssr/nopythonpip.png)

那么就需要安装pip。首先执行命令：

```bash
yum -y install epel-release
```

之后再次执行：

```bash
yum install python-pip
```

然后更新和安装SS。

```bash
pip install --upgrade pip
pip install shadowsocks
```

这样，Shadowsocks的服务器就搭建好了。

### 配置

以下是配置流程；

**新建配置文件：**

```bash
vi /etc/shadowsocks.json
```

> 注：linux的vim命令用于编辑文件，详细说明见：
> [**[Linux/Ubuntu] vi/vim 使用方法讲解**](http://www.cnblogs.com/emanlee/archive/2011/11/10/2243930.html)；

按快捷键 `i` 编辑该文件，并粘贴以下内容（建议先在文本文档中编辑好内容）：

```bash
{
"server":"你的服务器ip地址",
"server_port":8388,
"local_address": "127.0.0.1",
"local_port":1080,
"password":"你设置的密码",
"timeout":300,
"method":"aes-256-cfb",
"fast_open": false
}
```

检查无误后，按快捷键 `ESC` ，并输入

```bash
:wq!
```

之后回车 `Enter` 保存文件；

![启用ss服务器](http://upload-images.jianshu.io/upload_images/1018963-ad1dcf3dd2857fed.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 启动

使用上述配置启用Shadowsocks服务器：

```bash
ssserver -c /etc/shadowsocks.json -d start
```

至此SS服务器已经配置完成。

如果此时报错：`undefined symbol: EVP_CIPHER_CTX_cleanup`，则按以下方式解决：

### 报错

openssl升级到1.1.0以上版本，导致shadowsocks2.8.2启动报`undefined symbol: EVP_CIPHER_CTX_cleanup`错误，如下：

```bash
INFO: loading config from ss.json 
2016-12-14 22:47:50 INFO loading libcrypto from libcrypto.so.1.1 
Traceback (most recent call last): 
File “/usr/local/bin/sslocal”, line 11, in 
sys.exit(main()) 
File “/usr/local/lib/python2.7/dist-packages/shadowsocks/local.py”, line 39, in main 
config = shell.get_config(True) 
File “/usr/local/lib/python2.7/dist-packages/shadowsocks/shell.py”, line 262, in get_config 
check_config(config, is_local) 
File “/usr/local/lib/python2.7/dist-packages/shadowsocks/shell.py”, line 124, in check_config 
encrypt.try_cipher(config[‘password’], config[‘method’]) 
File “/usr/local/lib/python2.7/dist-packages/shadowsocks/encrypt.py”, line 44, in try_cipher 
Encryptor(key, method) 
File “/usr/local/lib/python2.7/dist-packages/shadowsocks/encrypt.py”, line 83, in init 
random_string(self._method_info[1])) 
File “/usr/local/lib/python2.7/dist-packages/shadowsocks/encrypt.py”, line 109, in get_cipher 
return m[2](method, key, iv, op) 
File “/usr/local/lib/python2.7/dist-packages/shadowsocks/crypto/openssl.py”, line 76, in init 
load_openssl() 
File “/usr/local/lib/python2.7/dist-packages/shadowsocks/crypto/openssl.py”, line 52, in load_openssl 
libcrypto.EVP_CIPHER_CTX_cleanup.argtypes = (c_void_p,) 
File “/usr/lib/python2.7/ctypes/init.py”, line 375, in getattr 
func = self.getitem(name) 
File “/usr/lib/python2.7/ctypes/init.py”, line 380, in getitem 
func = self._FuncPtr((name_or_ordinal, self)) 
AttributeError: /usr/lib/x86_64-Linux-gnu/libcrypto.so.1.1: undefined symbol: EVP_CIPHER_CTX_cleanup
```

这个问题是由于在openssl1.1.0版本中，废弃了EVP_CIPHER_CTX_cleanup函数，如官网中所说：

```bash
EVP_CIPHER_CTX was made opaque in OpenSSL 1.1.0. As a result, EVP_CIPHER_CTX_reset() appeared and EVP_CIPHER_CTX_cleanup() disappeared. 

EVP_CIPHER_CTX_init() remains as an alias for EVP_CIPHER_CTX_reset().
```

修改方法是：

用vim打开文件：`vim /usr/local/lib/python2.7/dist-packages/shadowsocks/crypto/openssl.py`，注意该路径请根据自己的系统情况自行修改，如果不知道该文件在哪里的话，可以使用find命令查找文件位置。

跳转到52行（shadowsocks2.8.2版本，其他版本搜索一下cleanup），进入编辑模式`vim`，将第52行`libcrypto.EVP_CIPHER_CTX_cleanup.argtypes = (c_void_p,)` 改为`libcrypto.EVP_CIPHER_CTX_reset.argtypes = (c_void_p,)`，然后再次搜索`cleanup`（全文件共2处，此处位于111行），将`libcrypto.EVP_CIPHER_CTX_cleanup(self._ctx)` 改为`libcrypto.EVP_CIPHER_CTX_reset(self._ctx)`，保存并退出。

启动shadowsocks服务：`service shadowsocks start` ，问题解决。

## PART5：开机启动

以下我们将配置Shadowsocks服务器开机启动。

### Ubuntu 16.04之前版本

编辑 `/etc/rc.local` 文件：

```bash
sudo vi /etc/rc.local
```

在 `exit 0` 这一行的上边加入如下

```bash
/usr/local/bin/ssserver -c /etc/shadowsocks.json -d start

```

保存后退出。

![编辑开机启动](http://upload-images.jianshu.io/upload_images/1018963-c9a846f1c7669f8e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### Ubuntu 16.04之后版本 及 CentOS 7.x系统

Ubuntu 16.04之后的版本系统，以及CentOS 7.x系统都是用systemd来管理系统，相关配置会有差异，详细内容可参考以下说明：

- [配置Ubuntu 16.04及以后版本的开机启动方案](ubuntu4ss.md)

这里不再重复了。

此后，Shadowsocks服务就会跟随服务器自动启用了。

## PART6：客户端配置

Shadowsocks的客户端配置非常简单，见下图所示：

![ss_client](http://upload-images.jianshu.io/upload_images/1018963-ff1718e892fd6647.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

服务器IP、服务器端口和密码就是你在PART4里面配置的内容；填上即可；

然后确定，并在右下角小飞机处右键、弹出菜单启用Shadowsocks；

![ss_client2](http://upload-images.jianshu.io/upload_images/1018963-c58b9221d456d136.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## PART7：参考资料

本文参考了以下资料，排名不分先后：

- [用正确的姿势上网：Shadowsocks，身轻如燕；](http://www.ulumen.com/shadowsocks-light-weight-tunnel/)
- [[Linux/Ubuntu] vi/vim 使用方法讲解；](http://www.cnblogs.com/emanlee/archive/2011/11/10/2243930.html)
- [使用Digital Ocean和shadowsocks来科学上网；](https://segmentfault.com/a/1190000002511795)
- [ubuntu 服务器搭建 Shadowsocks 服务；](http://blog.csdn.net/hanshileiai/article/details/49302865)
- [Shadowsocks@wiki.archlinux.org；](https://wiki.archlinux.org/index.php/Shadowsocks_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))
- [利用shadowsocks实现OpenWRT路由器的自动翻墙；](http://www.tuicool.com/articles/nAJfYv)
- [DigitalOcean免费VPS申请试用教程；](http://jingyan.baidu.com/article/29697b91c09710ab20de3c86.html)

