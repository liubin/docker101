# Lesson-01 课程简介及环境设置

# 安装

```
# 第一次启动虚拟机
[host]$ vagrant up
[host]$ vagrant ssh

# 升级系统并重启
[docker101vm]$ sudo yum -y update

# 重启后重新编译 VirtualBox Guest Additions
[docker101vm]$ sudo yum install -y gcc kernel-devel-3.10.0-229.11.1.el7.x86_64


# 重启虚拟机
[docker101vm]$ exit
[host]$ vagrant reload
[host]$ vagrant ssh

# 重新编译 VirtualBox Guest Additions
[docker101vm]$ sudo /etc/init.d/vboxadd setup

# 再次重启即可
```

安装Docker（docker-engine）

```
$ sudo curl -sSL https://get.docker.com/ | sh
+ sudo -E sh -c 'sleep 3; yum -y -q install docker-engine'
Failed to set locale, defaulting to C
warning: /var/cache/yum/x86_64/7/docker-main-repo/packages/docker-engine-1.8.1-1.el7.centos.x86_64.rpm: Header V4 RSA/SHA1 Signature, key ID 2c52609d: NOKEY
Public key for docker-engine-1.8.1-1.el7.centos.x86_64.rpm is not installed
Importing GPG key 0x2C52609D:
 Userid     : "Docker Release Tool (releasedocker) <docker@docker.com>"
 Fingerprint: 5811 8e89 f3a9 1289 7c07 0adb f762 2157 2c52 609d
 From       : https://yum.dockerproject.org/gpg

If you would like to use Docker as a non-root user, you should now consider
adding your user to the "docker" group with something like:

  sudo usermod -aG docker vagrant

Remember that you will have to log out and back in for this to take effect!

```

启动
```
sudo systemctl start docker
```

自启动
```
sudo systemctl enable docker
```


```
sudo vi /lib/systemd/system/docker.service
/usr/bin/docker daemon --registry-mirror=http://liubin.m.alauda.cn -H fd://

sudo systemctl daemon-reload
sudo systemctl restart docker
```

# 参考资料

- 灵雀云镜像加速设置方法

http://docs.alauda.cn/?page_id=137

- March 21, 2013 Solomon gives Docker lightning talk a PyCon US

https://www.youtube.com/watch?t=27&v=9xciauwbsuo

- Operating-system-level virtualization implementations

https://en.wikipedia.org/wiki/Operating_system-level_virtualization#Implementations

- hashicorp公司主页

https://hashicorp.com/

- Docker toolbox

https://www.docker.com/toolbox




