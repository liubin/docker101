# Docker入门与实践

Lesson-03 理解Docker镜像
刘斌@OneAPM
2015年8月

# 本节概要

- Docker存储原理
- 镜像构造
- 管理镜像
- 手工构建镜像

# Docker存储引擎

- CoW
- AUFS
- Device Mapper
- BTRFS
- OverlayFS
- ZFS

# 什么是Docker镜像

- 容器的模板
- 分层

# 分层的优势

- 轻量（磁盘空间）
- 缓存，提高构建速度

# Repository

- 存放镜像的仓库
- 有一个名字
- 一个镜像可以属于多个仓库
- docker commit
- docker build -t
- docker tag

# 仓库名称

- [registry[:port]/][user_name/](repository_name:version_tag)
- 默认index.docker.io:80

# 查看Docker镜像

- docker images
- REPO/TAG/ID

# 查看Docker镜像

- docker history
- docker inspect

# 拉取和推送镜像

- docker pull
- docker push

# 常用镜像命令

- 删除镜像
- push
- tag

# push到灵雀云

- 创建镜像仓库
- docker login
- docker push

# 共享镜像

- 镜像存储
- 官方
- 免费+收费
- 或私有Registry

# 拷贝到其他机器

- Docker iamge = tar文件
- export/import
- save/load

# export/import

- export：整个mount，将rootfs打成一个tar包
- import：tar包解包而已
- 镜像层级关系和metadata丢失
- 不能共享层文件，占用大量磁盘空间
- 能导入物理机或者虚拟机的rootfs

# save/load

- 按镜像层分别打tar包
- 一层层mount，打包
- 先打base
- mount下一层，打包差分
- 记录metadata
- 将结果再打成一个tar包

# 通过docker commit命令构建镜像

- docker run -it base bash
- # yum update && yum install xxx && exit
- docker commit 

# docker commit 参数

- -m --message
- -a --author
- -c --changes

# commit -c可覆盖参数

- CMD、ENTRYPOINT
- ENV
- EXPOSE
- ONBUILD
- USER
- VOLUME
- WORKDIR


# 课后作业

- docker pull
- docker history|inspect
- docker images/rmi
- docker commit


