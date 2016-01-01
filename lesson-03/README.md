# lesson-3 理解Docker镜像

# 常用操作

## 1. pull/push

```
docker search wordpress | head -10

docker pull wordpress

docker images

docker images wordpress

docker history wordpress

docker inspect wordpress

docker inspect  -f "{{.Config}}" wordpress

docker inspect wordpress | jq ".[0].Config"

docker inspect wordpress | jq ".[0].Config.Cmd"

docker inspect wordpress | jq ".[0].Config.Entrypoint"


docker login index.alauda.cn

docker tag busybox index.alauda.cn/liubin/test

docker images

docker push index.alauda.cn/liubin/test

docker rmi index.alauda.cn/liubin/test

```

## 2. 通过commit构建镜像

```
docker run -it centos bash
```

进入容器安装Nginx


```
# rpm -ivh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
# yum install -y nginx

# vi /etc/nginx/nginx.conf
add "daemon off;"

```

退出容器后，提交定制容器

```
cid=$(docker ps -lq)

docker commit --change="CMD [\"/usr/sbin/nginx\"]" --message="Docker101 Nginx image" --author="bin liu" $cid liubin/nginx

```

可以使用`docker inspect`命令来查看新创建的镜像的详细信息


```
docker inspect liubin/nginx

```

启动容器并确认

```
docker run --name nginx -d -p 80:80 liubin/nginx
```

## 3. save

```
mkdir /tmp/bb && cd /tmp/bb

docker save --output busybox.tar busybox

tar xf busybox.tar

ls

```

# 存储

## 检验AUFS镜像存储


```
# docker run -it centos bash

[container]# echo "hello world" > /root/test.txt

[container]# echo "hello world" >> /etc/redhat-release
[container]# rm /etc/bashrc
 
[container]# exit 

# docker diff $c

# cd /var/lib/docker/aufs/diff/$c
# pwd
/var/lib/docker/aufs/diff/ee0e5435dc4cb670517c973e88f9d66d19fb617305463b982482ecb56c12e4ce

# ls

# ls root/
test.txt

# cat root/test.txt 

hello world
```

## 检验DM镜像存储

### 常用命令

```
docker info

losetup

dmsetup ls

docker run -d centos /bin/sh -c "while true; do echo Hello world; sleep 1; done"

dmsetup ls

```

启动一个容器后

```
[root@docker101vm ~]# docker run centos /bin/bash -c 'echo "hello world" > /root/test.txt'

[root@docker101vm ~]# c=$(docker ps -alq  --no-trunc)


[root@docker101vm ~]# cat /var/lib/docker/devicemapper/metadata/$c | jq .

{
  "device_id": 579,
  "size": 10737418240,
  "transaction_id": 1089,
  "initialized": false
}


[root@docker101vm ~]# device_id=579
[root@docker101vm ~]# size=10737418240
[root@docker101vm ~]# pool=docker-253:1-134414442-pool

[root@docker101vm ~]# dmsetup ls

[root@docker101vm ~]# dmsetup create myvol --table "0 $(($size / 512)) thin /dev/mapper/$pool $device_id"

[root@docker101vm ~]# dmsetup ls
[root@docker101vm ~]# mkdir /mnt-test
[root@docker101vm ~]# mount /dev/mapper/myvol /mnt-test
[root@docker101vm ~]# ls /mnt-test/
id  lost+found  rootfs
[root@docker101vm ~]# cat /mnt-test/rootfs/root/test.txt 
hello world


[root@docker101vm ~]# mount | grep test
/dev/mapper/myvol on /mnt-test type ext4 (rw,relatime,seclabel,stripe=16,data=ordered)
[root@docker101vm ~]# umount /mnt-test
[root@docker101vm ~]# mount | grep test
[root@docker101vm ~]# dmsetup remove myvol
[root@docker101vm ~]# 

```

# 视频地址

* [认识Docker镜像](http://www.kaikeba.com/kkb/kaikeba/content_video.html?vId=2727)
![](http://video.kk8.cdn.bj.xs3cnc.com/2c/i/covers/Docker8.jpg)
* [手工构建Docker镜像](http://www.kaikeba.com/kkb/kaikeba/content_video.html?vId=2728)
![](http://video.kk8.cdn.bj.xs3cnc.com/2c/i/covers/Docker9.jpg)

