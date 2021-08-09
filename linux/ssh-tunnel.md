<center style="font-weight: bold; font-size: 30px;">SSH隧道</center>

### 一、修改sshd相关配置

```bash
vim /etc/ssh/sshd_config
```
```ini
# 删除PermitRootLogin配置的注释符号并设为yes，允许root账号登录
PermitRootLogin yes

# 删除PasswordAuthentication配置的注释符号并设为no，进制使用密码登录，只能使用密钥登录
PasswordAuthentication no
```
```bash
systemctl restart sshd
```



### 二、安装autossh

autossh可以实现SSH自动重连的功能

```bash
yum install autossh -y
```



### 三、本地监听/正向代理

> autossh -M [心跳包端口] -fCNL [本地IP，可省略]:[本地端口]:[远端IP]:[远端端口] [ssh用户名@远端域名或IP]

```bash
autossh -M 10001 -fCNL 10.1.1.115:10002:127.0.0.1:3306 root@www.baidu.com
```

- -M：autossh的参数，用于监控SSH连接状态，autossh会向该端口发送测试数据，若无返回则认为SSH连接中断，此时会自动重连直至成功，类似心跳包；
- -f：SSH的参数，将SSH放置在后台运行；
- -C：SSH的参数，用于数据压缩；
- -N：SSH的参数，不执行远程指令，请务必配置该参数；
- -L：SSH的参数，将本地的数据转发到远端。

**以上命令的意义为：在本地监听TCP 10001端口，用于心跳包的发送与接收；在后台运行SSH端口转发服务，在本地IP 10.1.1.115上监听10002这个端口，并将该端口的数据转发到tc-cloud.t.com的3306端口。**

如果使用本地监听，建议把本地IP添加在命令中，以提高安全性。



### 四、远端监听/反向代理

> autossh -M [心跳包端口] -fCNR [远端IP，可省略]:[远端端口]:[本地IP]:[本地端口] [ssh用户名@远端域名或IP]

```bash
autossh -M 10003 -fCNR 127.0.0.1:10004:127.0.0.1:8081 root@www.baidu.com
```

- -R：SSH的参数，将远端的数据转发到本地。

**以上命令的意义为：在本地监听TCP 10003端口，用于心跳包的发送与接收；在后台运行SSH端口转发服务，在tc-cloud.t.com的IP 127.0.0.1上监听10004这个端口，并将该端口的数据转发到本地127.0.0.1的80端口。**



### 五、检查

通过以下命令即可查看相关进程

```bash
ps -aux | grep ssh | grep 100
```

或者直接查看端口

```bash
ss -alpn | grep 22
```

