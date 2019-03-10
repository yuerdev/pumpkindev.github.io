- 首先检查ppp是否开启

- ```
  cat /dev/ppp
  ```

  若开启，显示：No such file or directory 或者 No such device or address,如果显示No such device or address则表示继续

- 安装组件（ppp，iptables，pptpd）

```
yum install ppp iptables pptpd
```

- 配置

 

##### 　　1.编辑pptpd.conf

 

```
　　vim /etc/pptpd.conf
```

　　然后找到localip，remoteip，修改如下

```
localip 192.168.0.1
remoteip 192.168.0.234-238,192.168.0.245
```

 

　　**2.编辑options/pptpd**

　　

```
vim /etc/ppp/options.pptpd
```

　　找到对应行,修改dns

```
ms-dns 8.8.8.8
ms-dns 8.8.4.4
```

 

　　**3.编辑/etc/ppp/chap-secrets设置登录账号密码**

```
vim /etc/ppp/chap-secrets
```

　　按照格式输入配置参数

　　　　例：用户名　　pptpd　　密码　　*        //每个字段之间用tab键隔开  *表示允许所有ip

　　**4.修改内核参数，运行下面命令编辑sysctl.conf**

```
vim /etc/sysctl.conf
```

　　在打开的sysctl.conf文件中末尾添加下面一行代码，使内核支持转发

```
net.ipv4.ip_forward=1
```

　　运行下面命令使内核修改生效：

```
sysctl -p
```

 

　　**5.开启转发规则**

转发规则有两种：

 

(1)XEN架构：

```
iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -o eth0 -j MASQUERADE
```

 

 

(2)OpenVZ架构：

```
iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -j SNAT --to-source VPS公网IP  //VPS公网IP 要换成你服务器的IP 比如 119.24.40.64
```

 

选择其中一条命令输入即可，这里我输入的是第一条

 

终端输入命令：iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -o eth0 -j MASQUERADE

　　**6.编辑rc.local文件 添加转发规则**

1.添加文件权限

```
chmod +x /etc/rc.d/rc.local
```

2.编辑

```
vi /etc/rc.d/rc.local
```

添加下面两个架构其中的一个代码：

1）XEN架构：

```
iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -o eth0 -j MASQUERADE
```

 

 

2）OpenVZ架构：

```
iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -j SNAT --to-source VPS公网IP
```