# Docker入门与实践

Lesson-11 Docker Swarm
刘斌

# 本节概要

- Swarm简介
- 动手试用Swarm
- Swarm深入讲解

# 什么是Swarm

Docker Swarm is native clustering for Docker. It allows you create and access to a pool of Docker hosts using the full suite of Docker tools. 

![](../lesson-01/images/docker-swarm-logo.png)

# Docker集群软件出现的背景

- 应用架构变复杂
- 高可用、大规模应用的普及
- 资源池化

# 什么是Swarm

- 原生Docker集群
- 跨主机创建容器
- 兼容Docker Remote API
- BETA中

# 什么是Swarm

- 资源抽象层
- 原则：可交换，可插拔，通过API
- 第三方或自己定制的调度组件

# 架构

- Manager 节点
- Agent 节点

# 实验环境

- OS X
- Docker Machine


# 实验步骤

- 拉取Swarm镜像
- 通过swarm create获得集群id
- 创建swarm集群（及master节点）
- 创建集群Agent节点

# Swarm组件解析

- Discovery
- Scheduler

# 发现（Discovery）组件

- 让节点能找到所要加入的集群

# 发现组件的实现方式

- 基于Docker Hub的发现服务
- 静态文件（file参数）
- etcd
- consul
- zookeeper
- 静态服务器列表（nodes参数）
- 定制Backends

# 自定义服务发现

- DiscoveryService接口
- Initialize
- Fetch
- Watch
- Register

# Scheduler组件

- Strategies
- Filters

# Strategies

- Ranking（打分）
- swarm manage --strategy

# 目前支持的策略

- spread（默认）
- binpack
- random

# spread & binpack

- 按照可用CPU、内存以及运行中容器数为节点打分
- spread：最少容器运行 -> 容器平均分布
- binpack：最拥挤 -> 避免碎片化，最大限度利用资源

# Filters

- Constraint
- Affinity
- Port
- Dependency
- Health

# Constraint Filter

- 指定某一节点
- key-value tags
- storage=ssd
- storage=disk

# Constraint Filter

- docker -d --label storage=ssd
- docker run -e constraint:storage==ssd

# 标准约束Standard Constraints

- node ID or node Name (using key “node”)
- storagedriver
- executiondriver
- kernelversion
- operatingsystem

# 亲和度（Affinity）filter

- 通过指定关系的亲密程度来选择节点
- 指定容器或镜像
- 或者label
- 确保容器运行于同一网络节点上

# Container affinity

- 与指定的容器ID或name运行在同一节点

# Container affinity

```bash
docker run --name frontend
docker run -e affinity:container==frontend
```

# Image affinity

- -e affinity:image==redis
- -e affinity:image==06a1f75304ba

# Container Label affinity

```bash
docker run --label com.example.type=frontend
docker ps  --filter "label=com.example.type=front"
docker run -e affinity:com.example.type==frontend 
```

# Filter表达式语法

- constraint:node==node1
- constraint:node!=node1
- constraint:region!=us*
- constraint:node==/node[12]/
- constraint:node==/node\d/
- constraint:node!=/node-[01]/

# Soft Affinities/Constraints

- Affinities/Constraints 不满足，容器不会被启动
- Soft Affinities/Constraints （~）：不满足则丢弃约束
- -e affinity:image==~redis
- -e constraint:region==~us*
- -e affinity:container!=~redis*

# Port Filter

- 因为Port是稀缺且排他的资源

# Dependency Filter

- 两个容器必须要在同一node工作
- 如果不能找到满足条件的node，则拒绝创建新容器

# Dependency Filter 类型

- Volumes: --volumes-from=dependency
- Links: --link=dependency:alias
- Network stack: --net=container:dependency

# Health Filter

- 避免容器被分配到不健康的节点



