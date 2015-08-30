# Docker入门与实践

Lesson-04 使用Dockerfile构建镜像
刘斌@OneAPM
2015年8月

# 本节概要

- 掌握构建镜像的方式
- docker build命令
- Dockerfile语法

# 构建镜像的方式

- docker commit
- Dockerfile
- Packer类

# Dockerfile是什么

- 构建镜像的说明书
- DSL语法
- docker inspect

# 为什么用Dockerfile

- 版本控制（追踪和回滚）
- 公开透明
- 一定程度的可重现

# 使用Dockerfile构建的流程

- 编写Dockerfile
- 执行docker build命令

# 演示

- 构建Ruby镜像


# docker build命令

- docker build -t liubin/someimg[:sometag] path
- path: 本地路径、远程URL和 "-"

# 从STDIN接收压缩数据

- docker build - < context.tar.gz
- bzip2/gzip/xz

# 构建环境（Build Context）

- 包含Dockerfile的文件夹下的所有内容
- ADD file

# 构建环境支持git仓库

- git clone --depth 1 --recursive
- 还能指定git仓库的分支和目录
- https://somegit.com/repo.git#fa3424fa:path1


# Dockerfile指令格式

- 不区分大小写，指令习惯大写
- 按顺序读取
- 以#开头的为注释

# Dockerfile指令格式

```bash
# Comment
INSTRUCTION arguments
```

# ENV变量

```bash
ENV foo /bar
WORKDIR ${foo}   # WORKDIR /bar
ADD . $foo       # ADD . /bar
```

# 可以使用ENV变量的指令

- ADD/COPY
- WORKDIR
- EXPOSE
- VOLUME

# cache

- 缓存加快速度
- --no-cache

# 也能限制build过程使用的资源

- -m
- -c

# Tips： Dockerfile只能命名为Dockerfile？

- docker build -f other-docker-file

# .dockerignore

- 等于 .gitignore
- 指定不需要发送到daemon的文件（夹）

# .dockerignore

```bash
*/temp*
*/*/temp*
temp?
*.md
!LICENSE.md
```


# FROM指令

- FROM <image>
- FROM <image>:<tag>
- FROM <image>@<digest>


# MAINTAINER指令

- MAINTAINER <name>

# RUN指令

- RUN cmd arg （/bin/sh -c）
- RUN ["cmd", "arg1", "arg2"] （exec格式）
- RUN ["/bin/bash", "-c", "echo hello"]
- exec格式需使用双引号而不是单引号

# 状态修改

- 下载、安装文件
- 创建目录、文件
- 拷贝、移动文件

# 状态修改

- cd
- ls
- pwd

# 测试

- RUN [ "echo", "$HOME" ]

# 测试

- RUN [ "sh", "-c", "echo", "$HOME" ]

# 建议

- 多条换行写
- 避免单行的RUN apt-get update
- 避免类似apt-get upgrade等命令

# 建议

```bash
RUN apt-get update && apt-get install -y \
    package-bar \
    package-baz \
    package-foo
```

# LABEL

- 镜像元数据
- LABEL <key>=<value> <key>=<value> <key>=<value> ...
- LABEL "com.example.vendor"="ACME Incorporated"
- 继承父镜像标签
- 建议一行写多个label，节省镜像层

# ENTRYPOINT指令

- ENTRYPOINT command param1 param2 (shell form)
- ENTRYPOINT ["cmd", "param1", "param2"] (exec form)
- docker run image some-command
- 可通过docker run --entrypoint覆盖
- 最后一条ENTRYPOINT指令有效

# Shell形式

- 忽略命令行参数和CMD指令

# Exec形式

- 不启动shell

# CMD指令

- 指定容器默认启动程序
- CMD ["executable","param1","param2"]
- CMD ["param1","param2"]
- CMD command param1 param2 (shell form)
- 只有最后一条CMD指令生效


# Dockerfile 常用设计模式

- 必须都用JSON数组格式

# Dockerfile 常用设计模式

```bash
ENTRYPOINT ["s3cmd"]
CMD ["--help"]

$ docker run s3cmd

$ docker run s3cmd ls s3://mybucket
```

# EXPOSE

- 仅是声明
- 不会自动打开，-p或-P
- EXPOSE <port> [<port>...]

# ENV

- ENV <key> <value>
- ENV <key>=<value> ...
- 继承性

# ENV示例

```bash
ENV myName="John Doe" myDog=Rex\ The\ Dog \
    myCat=fluffy
and

ENV myName John Doe
ENV myDog Rex The Dog
ENV myCat fluffy
```

# 减少不必要的ENV

- RUN <key>=<value> <command>

# ADD

- 向镜像添加文件

- ADD <src>... <dest>
- ADD ["<src>",... "<dest>"]
- 文件、文件夹和URL

# <src>

- 文件需要位于 build context下
- src可包含通配符（filepath.Match）
- src为URL，文件名可以从URL推导
- src为文件夹，所有文件夹内容被拷贝
- 为压缩（gzip/bzip2/xz）文件，将会被解开（tar -x）

# <src>

```bash
ADD hom* /mydir/
ADD hom?.txt /mydir/
```

# <dest>

- 绝对路径
- 相对于WORKDIR的路径
- 新文件（夹）的UID=0，GID=0
- <src>为URL则新文件属性为600
- 如果指定了多个src，则dest必须以/结尾（否则出错）
- 建议dest如果为文件夹，则在后面都加上/

# <dest>

```bash
# adds "test" to `WORKDIR`/aDir/
ADD test aDir/
```


# src为URL

- ADD http://abc.com/somfile /file1
- ADD http://abc.com/somfile /file2/(somefile)
- ADD http://abc.com/ /file3
- ADD http://abc.com/ /file4/  （出错）
- 建议URL和dest都写明确，不要让Docker去推测


# COPY指令

- 和ADD一样
- COPY <src>... <dest>
- COPY ["<src>",... "<dest>"]

# ADD还是COPY？

- 推荐COPY
- 没有URL和自动解压
- 减少不确定性

- 不同类型的文件单独拷贝（最大效果利用缓存）
- 手工下载文件后删除（减小镜像尺寸）

- 尽量将全部操作都通过Dockerfile展现


# VOLUME

- VOLUME ["/data"]
- VOLUME /var/log
- VOLUME /var/log /var/db



# VOLUME测试

```bash
FROM ubuntu
RUN mkdir /myvol
RUN echo "hello world" > /myvol/greeting
VOLUME /myvol
```

# USER

- USER name or UID
- 影响RUN/CMD/ENTRYPOINT
- RUN groupadd -r postgres && useradd -r -g postgres postgres
- UID/GID不能保证可重现性，可显示指定
- 默认为root

# USER

```bash
USER user
USER user:group
USER uid
USER uid:gid
USER user:gid
USER uid:group
```

# WORKDIR

- WORKDIR /path/to/workdir
- 影响后续RUN/CMD/ENTRYPOINT/COPY/ADD指令
- 可多次使用


# 小测试

```bash
WORKDIR /a
WORKDIR b
WORKDIR c
RUN pwd
# pwd命令的结果？
```

# 小测试

- 结果是：
- /a/b/c


# 小测试

```bash
ENV DIRPATH /path
WORKDIR $DIRPATH/$DIRNAME
RUN pwd
# pwd命令的结果？
```

# 小测试

- 结果是：
- /path/$DIRNAME
- 只能替换Dockerfile设置的env环境变量

# WORKDIR使用建议

- 建议多使用绝对路径
- 使用WORKDIR替换"cd .."

# ONBUILD指令

- ONBUILD [INSTRUCTION]
- 子镜像构建触发器，不遗传，不能嵌套
- 在FROM中执行
- 不能通过ONBUILD执行FROM和MAINTAINER指令
- 就像在衍生镜像的FROM指令后执行


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


# 最佳实践

- 使用.dockerignore文件
- 减少层数
- 最小安装
- 注意build cache（apt-get -y update问题）

# 课后作业

- 基于centos:7构建自己的语言栈
- COPY
- WORKDIR/ENV
- EXEC/CMD
