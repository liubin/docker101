# lesson-2 运行Docker容器


# jq

```
# docker inspect centos | jq ".[0].Config.Cmd"
[
  "/bin/bash"
]

# docker inspect centos | jq ".[0].Config.Entrypoint"
null

```

# 命令测试

## 后台容器

shell 1:

```
docker run --name web -d -p 5000:5000 training/webapp python app.py
docker logs -f web
```

shell 2:

```
curl localhost:5000
```

## attach

```
docker run -d --name top1 centos top -b

docker attach top1
ctrl-c

docker run -d --name top2 centos top -b

docker attach --sig-proxy=false top2
ctrl-c

docker ps -a

```

## stop和kill的区别

```
docker run --name web -d -P training/webapp python app.py
docker stop web
docker rm web

docker run --name web -d -P training/webapp python app.py
docker kill web
docker rm web

```

## docker events

### shell 1

```
docker events
```

### shell 2

```
# Start a new container

JOB=$(docker run -d centos /bin/sh -c "while true; do echo Hello world; sleep 1; done")

docker attach --sig-proxy=false $JOB

docker stop $JOB

docker start $JOB

docker restart $JOB

docker kill $JOB

docker rm $JOB
```

### shell 1

```
docker events -f 'event=stop'
```

### shell 2

同上。

## docker restart/pause/unpause/top

shell 1:

```
docker events
```

shell 2

```
docker run -d --name alwayrestart --restart=always centos /bin/sh -c "while true; do echo Hello world; sleep 1; done"

docker logs -f alwayrestart
```
shell 3

```
docker top
docker pause
docker unpause
docker kill 
```


## docker diff

```
docker run --name container-diff centos sh -c 'echo "hello diff"> /test.txt'

docker diff container-diff

```

## docker run -v / -e

```
docker run -v `pwd`/test-data:/test_data -e DB_HOST=mysql centos bash
```

在容器内

```
# evn
# echo "text from container" > /test_data/hehe.txt
# exit
```

在主机内

```
cat ./test-data/hehe.txt
```

# docker log存储

shell 1:

```
id=$(docker run --name web -d -p 5000:5000 training/webapp python app.py)
tail -f /var/lib/docker/containers/$id/$id-json.log 

```

shell 2:

```
curl localhost:5000
```

# 通过commit构建镜像

```
$ sudo docker run -i -t centos bash
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
$ cid=$(sudo docker ps -l -q)
$ sudo docker commit --change="CMD [\"/usr/sbin/nginx\"]" --message="Docker101 Nginx image" --author="bin liu" $cid docker101/nginx

```

可以通过`docker ps --no-trunc -ql`命令得到刚创建的容器的ID

```
$ sudo docker ps --no-trunc -ql
573b314aa3846cc454ef90731629669eff646b1a6e4cc481a7a6b1423a639d12

$ sudo docker ps  -ql
573b314aa384

```

```
$ sudo docker images docker101/nginx

```

可以使用`docker inspect`命令来查看新创建的镜像的详细信息


```
$ sudo docker inspect docker101/nginx

```

启动容器并确认

```
$ sudo docker run -d -p 80:80 docker101/nginx
$ curl http://localhost/
```

