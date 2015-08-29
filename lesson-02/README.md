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

job=$(docker run -d centos /bin/sh -c "while true; do echo Hello world; sleep 1; done")

docker stop $job

docker start $job

docker restart $job

docker kill $job

docker rm $job
```

### shell 1

```
docker events -f 'event=stop'
```

### shell 2

同上。

## docker restart

shell 1:

```
docker events
```

shell 2

```
docker run -d --name alwayrestart --restart=always centos /bin/sh -c "while true; do echo Hello world; sleep 2; exit -1; done"
docker logs -f alwayrestart
```

## docker diff

```
docker run --name container-diff centos sh -c 'echo "hello diff"> /test.txt'

docker diff container-diff

```

## docker run -v / -e

```
docker run -it -v `pwd`/test-data:/test_data -e DB_HOST=mysql centos bash
```

在容器内

```
# env
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

# 添加dummy网络设备

```
# docker run -it --rm centos ip link add dummy0 type dummy
RTNETLINK answers: Operation not permitted
# docker run -it --rm --cap-add=NET_ADMIN centos ip link add dummy0 type dummy
```

# PID=host

```
docker run -it --pid=host centos bash

```

# net=host

```
docker run -it --net=host centos bash
```