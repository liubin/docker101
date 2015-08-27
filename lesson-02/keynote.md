# Docker入门与实践

Lesson-02 运行Docker容器
刘斌@OneAPM
2015年8月

# 本节概要

- docker命令的两个角色
- 理解容器
- 管理容器
- docker run命令

# docker命令

- 一个命令，两个角色
- daemon docker -d
- client docker run
- docker -D？

# Docker daemon(Server)

- -H=[]，监听接口（damain socket或者IP/端口）
- unix:///var/run/docker.sock
- -H tcp://host:2375
- --tls=true|false
- 《第一本Docker书》第8.5节
- fd： Systemd socket activation

# Docker Daemon参数

- 网络、安全、日志等
- -e, --exec-driver="native"
- -s, --storage-driver=""
- 存储驱动AUFS/device mapper/btrfs/zfs/overlay

# Docker execdriver

- libcontainer
- lxc

# Docker daemon(Server)

- $ sudo systemctl status docker
- $ sudo systemctl start docker
- $ sudo systemctl stop docker
- $ sudo systemctl restart docker
- $ sudo systemctl enable docker

# DOCKER_HOST环境变量

- docker -H tcp://0.0.0.0:2375 ps
- export DOCKER_HOST="tcp://0.0.0.0:2375" && docker ps

# 启动我们的第2个容器

- $sudo docker run centos:7 echo "I'm from container."

# 容器/镜像 ID

- 64位
- 容器
- 镜像
- 镜像层

# 常用容器命令

- docker run [OPTIONS] IMAGE[:TAG|@DIGEST] [COMMAND] [ARG...]
- docker ps
- docker rm

# Tips 自动删除停止的容器

- docker --rm 自动清除容器
- 和选项 -d 不兼容

# 进入容器内部

- $ sudo docker run -it centos:7 bash
- [root@e757c235c60c /]#
- hostname
- ip addr show eth0
- mount

# 容器的生命周期

- 创建、启动（docker create/run/restart）
- 停止（stop）
- 销毁（rm、kill）

# 前台容器和后台容器

- Foreground/Detached (-d)
- sudo docker run -it centos:7 bash

# 前台容器

- 启动容器内进程，并将当前控制台attach到该进程的标准输入、输出和错误。
- -t：分配一个伪终端（TTY）
- -i：保持STDIN打开
- -it：交互式控制台（Shell）

# 后台容器

- -d： 后台运行，无前台输入输出交互
- -P： 暴露所有端口

# 后台容器

```bash
docker run -d -P training/webapp python app.py
```

# docker attach

- attach 到一个Detached的容器上

# docker attach

```bash
docker run -d -P training/webapp python app.py
befb8d63f46b298644d32a ...
docker ps
docker attach befb
^C
```


# docker attach

- docker attach [OPTIONS] CONTAINER
- --no-stdin=false
- --sig-proxy=true 
- detach： CTRL-p CTRL-q (for a quiet exit)
- detach： CTRL-c if --sig-proxy == false
- SIGINT： CTRL-c if --sig-proxy == true（默认）


# 容器结束

```bash
docker run -d centos:7 echo "I'm from container."
61146a27f83a0b291cfa945135e25 ........
docker attach 611
You cannot attach to a stopped container, start it first
```

# 容器命名

- 长形UUID
- 短形UUID
- name：cocky_curie（scientists and hackers）
- docker run --name

# Tips： 如何获得容器ID？

- cid=$(sudo docker run -d -P training/webapp python app.py)
- sudo docker stop $cid
- --cidfile="some/path"

# 容器网络设置

- --dns=[]
- --mac-address=""
- --add-host=""
- --net="bridge"

# --net选项

- bridge
- none
- container:<name|id>
- host

# Tips 如何修改容器内的/etc/hosts文件？

```bash
docker run -it --add-host db-server:10.10.0.100
```

# restart机制

- none
- on-failure[:max-retries]
- always
- 每次重试间隔时间都翻倍，直到stop或rm -f

# docker rename

- docker rename OLD_NAME NEW_NAME

# docker diff

- docker diff CONTAINER
- A - Add
- D - Delete
- C - Change

# docker pause/unpause

- docker pause CONTAINER
- docker unpause CONTAINER

# docker top

- docker top CONTAINER [ps OPTIONS]

# docker cp

- docker cp CONTAINER:PATH HOSTDIR
- docker cp CONTAINER:PATH -

# docker create

- docker create [OPTIONS] IMAGE [COMMAND] [ARG...]
- then docker start
- 状态：created.

# docker create 用武之地

```bash
docker create -v /data --name data centos:7
240633dfbb98128fa77473d3d9018f6123b99c454b3251427ae190a7d951ad57
docker run --rm --volumes-from data centos:7 ls -la /data
total 8
drwxr-xr-x  2 root root 4096 Dec  5 04:10 .
drwxr-xr-x 48 root root 4096 Dec  5 04:11 ..
```

# docker events

- docker events [OPTIONS]
- create, destroy, die, export, kill, oom, pause, restart, start, stop, unpause
- untag, delete
- 指定时间段和过滤器

# 指定时间条件

- --since=""
- --until=""
- docker events --since 1378216169
- docker events --since '2013-09-03'
- docker events --since '2013-09-03T15:49:29'

# 指定filter

- -f / --filter
- container
- event
- image
- AND / OR

# 指定filter

- docker events -f 'event=stop'
- docker events -f 'image=ubuntu-1:14.04'
- docker events -f 'container=xx' -f 'container=yy'
- docker events -f 'container=xx' -f 'event=stop'

# 对容器进行资源限制-Memory

- -m, --memory="": 格式 <number><optional unit>（单位为b/k/m/g）
- --memory-swap="": 内存总量限制（memory+swap，单位同上）
- --oom-kill-disable=true|false

# 对容器进行资源限制-Memory

- docker run -ti -m 300M --memory-swap -1
- docker run -ti -m 300M （--memory-swap 300M）
- docker run -ti -m 300M --memory-swap 1G

# 对容器进行资源限制-Memory

- docker run -ti -m 100M --oom-kill-disable
- docker run -ti --oom-kill-disable <- 危险行为

# 对容器进行资源限制-CPU

- -c, --cpu-shares=0: CPU shares （相对权重）
- --cpu-period=0: 限制 CPU CFS (Completely Fair Scheduler)
- --cpuset-cpus="": 可用CPU的核(0-3, 0,1)
- --cpuset-mems="": 可利用的Memory节点 (0-3, 0,1)，NUMA系统可用
- --cpu-quota=0: 限制 CPU CFS用量

# 对容器进行资源限制-CPU share

- 所有容器的默认CPU周期（cycles）权重为1024
- 通过-c 或 --cpu-shares 设置为大于2的值
- 多个容器按比例分配CPU


# 对容器进行资源限制-CPU quota

- 选项 --cpu-quota 用来对容器的CPU使用做出限制
- 默认值0表示可以使用100%的CPU
- 单核CPU，50000表示可以使用50%的CPU


# 对容器进行资源限制-CPU period

- 默认完全公平调度程序CFS（Completely Fair Scheduler）周期为100ms
- 通过--cpu-period 可以限制容器的CPU使用
- 单位：microseconds，百万分之一，微秒
- 通常和--cpu-quota一起工作
- --cpu-period=50000 --cpu-quota=25000 -> 50% CPU

# 对容器进行资源限制-CPU set

- 指定运行所在CPU
- $ docker run -ti --cpuset-cpus="1,3" ...
- $ docker run -ti --cpuset-cpus="0-2" ...

# 对容器进行资源限制-Block I/O

- --blkio-weight
- 设置I/O权重，取值范围为10~1000
- 容器默认I/O权重为500
- $ docker run --blkio-weight 300

# 和Namespace相关的一些参数

- PID
- UTS
- IPC

# PID

- --pid=""，设置容器的PID namespace
- --pid=host

# UTS

- --uts=""，设置UTS namespace
- 'host'
- --net=host 不包括 --uts=host
- 容器名随host而变
- 在容器中修改host的主机名

# IPC

- --ipc=""，设置容器的IPC模式
- container:<name|id>
- host

# Linux capabilities

- 默认Docker容器没有特权“unprivileged”
- --privileged 允许容器访问所有设备（device）
- 或修改AppArmor/SELinux的配置
- 精细控制（--cap-add/--cap-drop）
- --device
- --lxc-conf
- docker run --device=/dev/snd:/dev/snd ...

# Linux capabilities示例

- SETPCAP： 修改进程
- SYS_MODULE： 加载/卸载内核模块
- SYS_ADMIN：系统管理
- SYS_TIME： 修改系统时间
- NET_ADMIN： 网络管理操作
- KILL： 跳过权限发送signal
- SETGID/SETUID
- SYSLOG
- CHOWN


# Linux capabilities 示例

```bash
docker run --cap-add=ALL --cap-drop=MKNOD ...
docker run -t -i --rm  ubuntu:14.04 ip link add dummy0 type dummy
RTNETLINK answers: Operation not permitted
docker run -t -i --rm --cap-add=NET_ADMIN ubuntu:14.04 ip link add dummy0 type dummy
```

# Logging驱动

- 容器可以指定logging驱动
- --log-driver=VALUE
- none
- json-file（默认，docker logs可用）
- syslog
- journald
- gelf： Graylog Extended Log Format (GELF)
- fluentd

# 能通过docker run覆盖的镜像参数

- Dockerfile的设置可覆盖
- FROM/MAINTAINER/RUN/ADD
- CMD/ENTRYPOINT/EXPOSE/ENV/VOLUME/USER/WORKDIR

# CMD

- docker run -it cnetos:7 bash

# ENTRYPOINT

- --entrypoint=""

# EXPOSE

- Dockerfile里唯一对网络设置选项
- -P或-p
- -p containerPort
- -p hostPort:containerPort
- -p ip:hostPort:containerPort
- -p ip::containerPort


# ENV

- 预设： HOME/HOSTNAME/PATH/TERM
- --link
- -e "deep=purple"

# VOLUME

- -v=[] [host-dir:]container-dir[:rw|ro]
- --volumes-from=""

# USER

- 默认为root
- -u="": Username or UID

# WORKDIR

- -w=""


# 课后作业

- 下载centos:7仓库
- docker run -it centos:7 bash
- docker ps -a
- docker rm





