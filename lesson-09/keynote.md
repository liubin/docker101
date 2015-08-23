# Docker入门与实践

Lesson-09 Docker Compose
刘斌

# 本节概要

- 什么是Docker Compose
- 使用Docker Compose构建多容器应用
- Docker Compose用法详解

# 什么是Docker Compose

- 定义和管理多Docker容器应用的工具
- 非常适合在开发、预演和CI环境下使用
- 目前不建议在生产环境

# Compose涵盖功能

- 定义、运行多容器应用
- 一个文件定义
- 一条命令启动
- 一条命了几乎能干任何事情,从启动到销毁

# 三步使用Compose

- 通过Dockerfile定义应用镜像
- 通过docker-compose.yml定义服务，容器在一个统一的隔离环境
- docker-compose up

# Compose管理整个应用的生命周期

- 构建镜像，启动停止服务
- 管理服务
- 管理日志
- 执行单条命令

# 启动第一个例子

```bash
$ docker-compose up -d
$ docker-compose run web env
$ docker-compose stop
```

# Compose命令说明

- build：构建或者重新构建服务
- kill：（SIGKILL）
- kill -s SIGINT
- logs：
- port
- ps
- pull
- restart
- rm


# Compose命令说明：run

- docker-compose run web python manage.py shell
- 自动创建volume
- link容器也会被创建（--no-deps禁止自动创建）
- 不会创建新的端口（--service-ports）

# Compose命令说明： scale

- docker-compose scale web=2 worker=3
- 然并卵

# Compose命令说明

- start
- stop
- rm

# Compose命令说明： up

- 构建/创建/启动/attach到服务的容器中
- 被Linked的服务会自动启动
- -d
- --no-recreate


# 容器links

- Compose使用容器links连接服务
- docker-compose run SERVICE env

# Compose参数说明：-f

- -f -file
- 默认 docker-compose.yml
- 向上级文件夹递归查找

# Compose参数说明：-p

- -p, –project-name NAME
- 默认为当前文件夹名。


# Compose的环境变量

- COMPOSE_PROJECT_NAME：默认为当前文件夹
- COMPOSE_FILE：配置文件，默认为docker-compose.yml
- DOCKER_HOST：默认为 unix:///var/run/docker.sock
- DOCKER_TLS_VERIFY：非空为启用TLS
- DOCKER_CERT_PATH：设置证书文件位置，默认为~/.docker


# Compose配置文件说明


- image
- build
- dockerfile
- command

# links

- db
- db:database
- redis

# external_links


# extra_hosts

- 等于--add-host参数
- "somehost:162.242.195.82"
- "otherhost:50.31.209.229"

# ports

- "3000"
- "49100:22"
- "127.0.0.1:8001:8001"
- ！！使用字符串形式

# expose

- expose但是不向host发布
- 只能在linked服务中使用

# volumes

- /var/lib/mysql
- cache/:/tmp/cache
- ~/configs:/etc/configs/:ro

# volumes_from

- service_name
- container_name

# environment

- RACK_ENV: development
- SESSION_SECRET:


# env_file

- 值或者list的形式
- env_file: .env


# extends

- 继承模板文件
- 可以覆盖模板中的设置
- extends:
-    file: common.yml
-    service: web

# extends

- links和volumes_from需要在本地定义
- 单值属性（image/command）： 覆盖

# extends 多值属性

- 串接： ports/expose/external_links/dns/dns_search
- 按key merge： environment/labels
- 按value merge： volumes/devices

# labels

- 容器的metadata
- 建议使用DNS标记法以防冲突
- com.example.description: "Accounting webapp"
- "com.example.description=Accounting webapp"

# log driver

- 等于docker run --log-driver
- 可以指定为json-file,syslog和none
- 默认为json-file

# net

- 等于 docker run --net参数
- net: "bridge"
- net: "none"
- net: "container:[name or id]"
- net: "host"

# 其他参数

- dns, dns_search
- pid
- cap_add, cap_drop


# 其他参数

```bash
cap_add:
  - ALL
cap_drop:
  - NET_ADMIN
  - SYS_ADMIN
security_opt:
  - label:user:USER
  - label:role:ROLE
```

# 和docker run一样的参数

- working_dir
- entrypoint
- user
- hostname
- mem_limit
- privileged
- restart
- cpu_shares
- cpuset


# Docker Compose的局限

- 面向单主机
- 不能解决网络和存储问题

