# Docker入门与实践

Lesson-02 运行Docker容器
刘斌@OneAPM
2015年8月

# 本节概要

- docker命令的两个角色
- 理解容器构造
- 管理容器生命周期
- docker run命令的用法

# docker命令

- 一个命令，两个角色
- daemon: docker daemon
- client: docker run/ps/rm/images
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

![](../lesson-01/images/docker-libcontainer-lxc.png)

# Docker daemon(Server)

- $ sudo systemctl status docker
- $ sudo systemctl start docker
- $ sudo systemctl stop docker
- $ sudo systemctl restart docker
- $ sudo systemctl enable docker

# DOCKER_HOST环境变量

- docker -H tcp://0.0.0.0:2375 ps
- export DOCKER_HOST="tcp://0.0.0.0:2375" && docker ps

# 设置灵雀云加速

```
[docker101vm]$ sudo vi /lib/systemd/system/docker.service

/usr/bin/docker daemon --registry-mirror=http://liubin.m.alauda.cn -H fd://

[docker101vm]$ sudo systemctl daemon-reload
```

# 演示 1

- 通过IP地址连接远程Docker
- docker -H tcp://0.0.0.0:2375 pull ubuntu

# 启动容器

- docker run [OPTIONS] IMAGE[:TAG|@DIGEST] [COMMAND] [ARG...]

# 演示 2

- 启动我们的第2个容器
- $ sudo docker run -it centos bash

# 演示 2：容器内部

- [root@e757c235c60c /]#
- hostname
- ip addr show eth0
- mount
- ps -ef
- mount
- exit
- docker ps -a

# 演示 3：容器/镜像 ID

- 64位
- 容器
- 镜像/镜像层
- 长形UUID（64位）
- 短形UUID(12位)
- 1位？

# 容器命名

- name：cocky_curie（scientists and hackers）
- docker run --name

# Tips： 如何获得容器ID？

- cid=$(docker run --name name1 -d -P --cidfile=/tmp/cid.lock training/webapp python app.py)
- docker stop $cid
- --cidfile="some/path"

# 容器的生命周期

- 创建、启动（docker create/run/restart）
- 停止（stop、kill区别？）
- 销毁（rm）

# 前台容器和后台容器

- Foreground/Detached (-d)
- sudo docker run -it centos bash

# 前台容器

- 启动容器内进程，并将当前控制台attach到该进程的标准输入、输出和错误。
- -t：分配一个伪终端（TTY）
- -i：保持STDIN打开
- -it：交互式控制台（Shell）

# 后台容器

```bash
docker run --name web -d -P training/webapp python app.py
docker port web
```

# 后台容器

- -d： 后台运行，无前台输入输出交互
- -P： 暴露所有端口
- -p： 精确指定端口

# docker attach

- attach 到一个Detached的容器上


# docker attach

- docker attach [OPTIONS] CONTAINER
- --no-stdin=false
- --sig-proxy=true
- detach： CTRL-p CTRL-q (for a quiet exit)
- detach： CTRL-c if --sig-proxy == false
- SIGINT： CTRL-c if --sig-proxy == true（默认）


# docker attach

- 演示5

# stop/kill

- stop，SIGTERM,超过-t(10s)则SIGKILL
- kill，SIGKILL
- kill -s, --signal="KILL"

# 常用容器命令

- docker stop/kill
- docker ps -a|-n --no-trunc
- docker rm

# Tips 自动删除停止的容器

- docker run --rm 自动清除容器
- 和选项 -d 不兼容

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

# 演示

- 使用host模式
- 检查网络设置
- /etc/hosts
- /etc/resolve.conf
- /etc/hostname
- --add-host测试


# docker events

- docker events [OPTIONS]
- create, destroy, die, export, kill, oom, pause, restart, start, stop, unpause
- untag, delete
- 指定时间段和过滤器
- 演示6

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
- docker events -f 'image=ubuntu:14.04'
- docker events -f 'container=xx' -f 'container=yy'
- docker events -f 'container=xx' -f 'event=stop'

# restart机制

- none
- on-failure[:max-retries]
- always
- 每次重试间隔时间都翻倍，直到stop或rm -f
- 演示7

# 其他一些docker命令

- docker rename/top/cp
- docker pause CONTAINER
- docker unpause CONTAINER
- docker diff

# docker create

- docker create [OPTIONS] IMAGE [COMMAND] [ARG...]
- then docker start
- 状态：created.

# docker create 用武之地

```bash
docker create -v /data --name data centos
240633dfbb98128fa77473d3d9018f6123b99c454b3251427ae190a7d951ad57
docker run --rm --volumes-from data centos ls -la /data
total 8
drwxr-xr-x  2 root root 4096 Dec  5 04:10 .
drwxr-xr-x 48 root root 4096 Dec  5 04:11 ..
```

# 常用选项

- 在镜像中指令有关
- -v
- -e
- -p
- -P

# ENTRYPOINT

- --entrypoint=""
- docker inspect centos | jq ".[0].Config.Entrypoint"

# EXPOSE

- Dockerfile里唯一对网络设置选项
- -P或-p
- -p 5000
- -p 6000:6000
- -p 127.0.0.1:7000:7000
- -p 127.0.0.1::7000

# ENV

- 预设： HOME/HOSTNAME/PATH/TERM
- --link
- -e "deep=purple"

# VOLUME

- -v=[] [host-dir:]container-dir[:rw|ro]
- --volumes-from=""

# 演示8

- 见 README.md

# USER

- 默认为root
- -u="": Username or UID

# WORKDIR

- -w=""


# Logging驱动

- 容器可以指定logging驱动
- --log-driver=VALUE
- none
- json-file（默认，docker logs可用）
- syslog
- journald
- gelf： Graylog Extended Log Format (GELF)
- fluentd

# 演示9

- 查看log
- /var/lib/docker/containers
- 容器停止后的log
- 见 README.md

# 对容器进行资源限制-Memory

- -m, --memory="": 格式 <number><optional unit>（单位为b/k/m/g）
- --memory-swap="": 内存总量限制（memory+swap，单位同上）
- --oom-kill-disable=true|false

# 对容器进行资源限制-Memory

- -m 300M --memory-swap -1
- -m 300M （--memory-swap 600M）
- 全部内存 = 2 * m，swap = m
- -m 300M --memory-swap 1G

# 对容器进行资源限制-Memory

- -m 100M --oom-kill-disable
- --oom-kill-disable <- 危险行为

# 对容器进行资源限制-CPU

- -c, --cpu-shares=0: CPU shares （相对权重）
- --cpu-period=0: 限制 CPU完全公平调度程序 (Completely Fair Scheduler)时间
- --cpu-quota=0: 限制 CPU CFS用量
- --cpuset-cpus="": 可用CPU的核(0-3, 0,1)
- --cpuset-mems="": 可利用的Memory节点 (0-3, 0,1)，NUMA系统可用
- 非一致性内存访问 NUMA（Non-Uniform Memory Access Architecture）
- NUMA = SMP（对称多处理） + MPP（大规模并行处理）
- 每个节点都有独立的CPU+内存，甚至I/O

# 对容器进行资源限制-CPU share

- 所有容器的默认CPU周期（cycles）权重为1024
- 通过-c 或 --cpu-shares 设置为大于2的值
- 多个容器按比例分配CPU

# 对容器进行资源限制-CPU quota

- 选项 --cpu-quota 用来对容器的CPU使用做出限制
- 默认值0表示可以使用100%的CPU

# 对容器进行资源限制-CPU period

- 默认完全公平调度程序CFS（Completely Fair Scheduler）周期为100ms
- 通过--cpu-period 可以限制容器的CPU使用
- 单位：microseconds，百万分之一，微秒
- 通常和--cpu-quota一起工作
- --cpu-period=50000 --cpu-quota=25000 -> 50% CPU

# 对容器进行资源限制-CPU set

- 指定运行所在CPU
- --cpuset-cpus="1,3" ...
- --cpuset-cpus="0-2" ...

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
- 容器名随host而变
- 在容器中修改host的主机名

# IPC

- --ipc=""，设置容器的IPC模式
- container:<name|id>
- host
- 演示10

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
- SETGID/SETUID：设置UID/GID
- SYSLOG：使用SYSLOG
- CHOWN：修改属主


# 课后作业

- 下载centos仓库
- docker run -it centos bash
- docker ps -a
- docker rm
- 尝试docker run的各种选项





