# Docker入门与实践

Lesson-10 Docker Machine
刘斌@OneAPM
2015年8月

# 本节概要

- 什么是Docker Machine
- 使用Machine创建Docker主机
- 使用Machine在Digital Ocean上创建主机
- Machine命令讲解

# 什么是Docker Machine

![](../lesson-01/images/docker-machine-logo.png)

# 背景

- 大量服务的provisioning
- 手工安装、升级的麻烦

# Docker Machine

- 创建Docker主机运行环境
- 创建服务器
- 配置Docker
- 配置客户端环境变量

# Docker Machine

- 远程控制主机：
- 启动、停止和重启
- 升级Docker
- 配置客户端环境变量以和Daemon通信

# 实验操作环境

- HOST： OS X
- Driver： VirtualBox、Digital Ocean

# 创建VirtualBox主机

```bash
docker-machine create --driver virtualbox dev
eval "$(docker-machine env dev)"
docker run busybox echo hello world
```

# 在云主机上使用Docker Machine

- 创建digitalocean token

# 在云主机上使用Docker Machine

```bash
docker-machine create \
    --driver digitalocean \
    --digitalocean-access-token xxxxxxxxxxx \
    --digitalocean-image centos-7-0-x64 \
    --digitalocean-region "sfo1" \
    --digitalocean-size "512mb" \
    do
```

# Digital Ocean 参数

- --digitalocean-access-token
- --digitalocean-image
- --digitalocean-region
- --digitalocean-size
- --digitalocean-ipv6
- --digitalocean-private-networking
- --digitalocean-backups

# 子命令说明：create

- create
- docker-machine create -d virtualbox

# 子命令说明：create

- --engine-insecure-registry registry.myco.com
- --engine-registry-mirror
- --engine-label
- --engine-storage-driver

# 子命令说明：create

- --engine-opt dns=8.8.8.8
- --engine-opt log-driver=syslog

# 子命令说明：create

- --swarm


# 子命令说明：env

- docker-machine env machinename
- eval "$(docker-machine env machinename)"

# 子命令说明：ls

- docker-machine ls --filter driver=virtualbox --filter state=Running
- driver（driver name）
- swarm（swarm master’s name）
- state（Running|Paused|Saved|Stopped|Stopping|Starting|Error）

# 子命令说明：ssh

- docker-machine ssh dev
- docker-machine ssh dev COMMAND
- docker-machine ssh dev -- df -h

# 子命令说明：其他

- start
- stop
- restart（=stop && start）
- rm

# 子命令说明：其他

- inspect （-f）
- ip
- upgrade
- scp


# 子命令说明：其他

- active
- config
- kill（强制停止）



# 课后作业

- 使用VirtualBox创建Docker主机
- 有条件的话可以在云服务器上创建Docker主机

