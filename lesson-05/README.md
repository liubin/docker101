# Lesson-05 构建私有镜像服务器

# 使用Registry

## 启动Registry

```
$ sudo docker run -d -p 5000:5000 \
        --restart=always --name registry registry:2
```

## 使用Registry

```
# sudo docker run -it centos:7 bash
[root@85198a3c97fc /]# vi /start.sh
[root@85198a3c97fc /]# chmod +x /start.sh 
[root@85198a3c97fc /]# /start.sh 
from private registry
[root@85198a3c97fc /]# cat /start.sh 
#!/bin/sh
echo "from private registry"

[root@85198a3c97fc /]# exit
exit
[root@docker101vm ~]# sudo docker commit 85198a3c97fc localhost:5000/testimg2
11b2d12f02fcd5564eeace11aaa03854da7a3427cf0a1168dc8c93fb83ba8b9d
[root@docker101vm ~]# sudo docker images
REPOSITORY                 TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
localhost:5000/testimg2    latest              11b2d12f02fc        9 seconds ago       172.2 MB

[root@docker101vm ~]# sudo docker push localhost:5000/testimg2
The push refers to a repository [localhost:5000/testimg2] (len: 1)
11b2d12f02fc: Image already exists 
7322fbe74aa5: Image successfully pushed 
c852f6d61e65: Image successfully pushed 
f1b10cd84249: Image already exists 
Digest: sha256:f991c80cb3ca59e6de3a3fa69c0217093bcbaacbafda7254bf237291b8e0f0f5
[root@docker101vm ~]# sudo docker run localhost:5000/testimg2 /start.sh
from private registry
[root@docker101vm ~]# curl http://localhost:5000/v2/testimg2/tags/list
{"name":"testimg2","tags":["latest"]}

```


# 认证

## Basic认证

### 创建证书

```
mkdir -p certs && openssl req \
  -newkey rsa:4096 -nodes -sha256 -keyout certs/domain.key \
  -x509 -days 365 -out certs/domain.crt

Country Name (2 letter code) [XX]:CN
State or Province Name (full name) []:
Locality Name (eg, city) [Default City]:Beijing
Organization Name (eg, company) [Default Company Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (eg, your name or your server's hostname) []:private-registry.com
Email Address []:

```


将证书拷贝到系统目录

```
mkdir -p /etc/docker/certs.d/private-registry.com:6000/

cp certs/domain.crt /etc/docker/certs.d/private-registry.com:6000/ca.crt

```

添加host记录

```
vi /etc/hosts

128.199.218.41 private-registry.com

```

### 创建用户和密码

```
docker run --entrypoint htpasswd registry:2 -Bbn liubin 12345678 > auth/htpasswd
```

### 启动Registry

```
docker run -d -p 6000:5000 --restart=always --name registry \
  -v `pwd`/auth:/auth \
  -e "REGISTRY_AUTH=htpasswd" \
  -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" \
  -e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd \
  -v `pwd`/certs:/certs \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
  -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
  registry:2
```

### login 并 push镜像

```
docker login private-registry.com:6000

docker tag busybox private-registry.com:6000/basic-auth-img

docker push private-registry.com:6000/basic-auth-img

docker stop registry

docker rm -v registry
```

## Token认证


### Portus项目

使用了 [Portus](http://suse.github.io/Portus/) 项目。

```
cd

git clone https://github.com/liubin/Portus.git

cd Portus
```

修改Docker守护进程参数

```
vi /lib/systemd/system/docker.service

ExecStart=/usr/bin/docker daemon --insecure-registry private-registry.com:5000 -H fd://
```

启动

```
./compose-setup.sh

```

### 注册用户


打开浏览器，注册用户，添加registry

- Name： private-registry.com
- Hostname： private-registry.com:5000（千万别忘记写端口号）

添加host记录（在主机上，如果用了Vagrant，需要在主机和Vagrant中进行）

```
127.0.0.1 private-registry.com
```


### 在命令行login

```
docker login -u liubin -p 12345678 -e liubin0329@gamil.com private-registry.com:5000

docker tag busybox private-registry.com:5000/liubin/busybox

docker push private-registry.com:5000/liubin/busybox

docker tag busybox private-registry.com:5000/busybox:tag1

docker push private-registry.com:5000/busybox:tag1


```

# 参考

* 项目地址

https://github.com/docker/distribution

* Registry API

https://docs.docker.com/registry/spec/api/

* 认证

  - https://docs.docker.com/registry/authentication/
  - https://github.com/docker/distribution/blob/master/docs/deploying.md

# 视频地址

* [构建私有镜像服务器](http://www.kaikeba.com/kkb/kaikeba/content_video.html?vId=2731)
![](http://video.kk8.cdn.bj.xs3cnc.com/2c/i/covers/Docker12.jpg)
* [为私有镜像服务添加Basic认证...](http://www.kaikeba.com/kkb/kaikeba/content_video.html?vId=2732)
![](http://video.kk8.cdn.bj.xs3cnc.com/2c/i/covers/Docker13.jpg)
* [为私有镜像服务添加Token认证...](http://www.kaikeba.com/kkb/kaikeba/content_video.html?vId=2733)
![](http://video.kk8.cdn.bj.xs3cnc.com/2c/i/covers/Docker14.jpg)
