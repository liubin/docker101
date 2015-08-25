# Docker入门与实践

Lesson-08 Docker Volume
刘斌@OneAPM
2015年8月

# 本节概要

- Docker Volume简介
- 使用数据卷进行数据管理
- 数据卷的备份和恢复

# 容器的数据去哪里了？

- 随着容器消失了。。。
- 容器启动不了了，如何备份出来？
- 如何共享数据？
- 等等问题

# 容器种类

- 有状态（stateful）
- 无状态（stateless）

# 数据卷

- 透过Union FS
- 持久化（persistent）
- 共享（share）

# 数据卷的用途、特点

- 拷贝base镜像中的数据到卷
- 在容器中共享和重用
- 绕过Union FS（CoW）
- 不会影响容器和镜像
- 和容器的生命周期无关

# 创建数据卷的方式

- docker run -v
- Dockerfile VOLUME 指令
- 默认在 /var/lib/docker 下

# 加载host文件夹为卷

- -v /src/webapp:/opt/webapp
- Windows 和 OS X 要 加上 Users前缀
- 不可在Dockerfile中使用

# 实验

- /host:/container
- /host存在文件
- /container存在文件
- 两者都存在文件


# 指定映射权限

```bash
/src/webapp:/opt/webapp:ro
```

# 也能mount单独文件

```bash
docker run -v ~/.bash_history:/.bash_history
```

# 数据卷容器

- data volume container
- 在容器间共享数据

# 创建数据卷容器

```bash
$ docker create -v /dbdata --name dbdata training/postgres /bin/true
$ docker run -d --volumes-from dbdata --name db1 training/postgres
$ docker run -d --volumes-from dbdata --name db2 training/postgres
$ docker run -d --name db3 --volumes-from db1 training/postgres
```


# Dangling数据卷

- 幽灵卷
- docker rm -v

# 数据备份、恢复和迁移

```bash
$ docker run --volumes-from dbdata -v $(pwd):/backup ubuntu tar cvf /backup/backup.tar /dbdata
```

# 课后作业

- 通过-v启动第一个容器
- 在该容器中对volume中的数据进行修改
- 启动第二个容器，使用--volumes-from
- 在第二个容器中查看volume中的数据

