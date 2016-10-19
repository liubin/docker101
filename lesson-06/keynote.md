# Docker入门与实践

Lesson-06 Docker Hub和自动构建
刘斌@Alauda
2015年8月

# 本节概要

- Docker Hub功能简介
- 在Docker Hub托管镜像
- 自动构建

# Docker Hub

- 分发组件
- 镜像托管
- 系统办公
- 免费和付费
- 工作流

# 主要功能

- 镜像仓库
- 自动构建（Automated Builds）
- Webhooks
- 组织（Organizations）
- GitHub/Bitbucket集成

# 官方镜像仓库

- Official Repositories
- docker-library/official-images
- docker-library/docs

# 官方镜像仓库标准

- 基础OS镜像
- 唾手可得的语言环境、软件
- Dockerfile规范良好、文档详尽
- 及时更新
- 软件交付渠道

# 和Docker Hub相关的命令

- login/logout
- pull/push
- search

# 自动构建

- GitHub/BitBucket


# 认证功能

- ~/.docker/config.json

# 自动构建过程

- 添加commit hook
- push后克隆到本地
- 在本地build
- 构建成功push镜像

# 自动构建优势

- 可信、可靠，保持最新
- 支持GitHub/Bitbucket公开或私有仓库
- 不能手工push镜像
- 一个仓库对应多个自动构建
- README.md即镜像说明


# Repository links

- 连接两个自动构建
- 一个仓库有变化，则自动触发另一个仓库的自动构建
- 不要循环连接


# 远程构建触发器

- Remote Build triggers
- 通过POST发送构建请求
- 5分钟只能请求1次
- 上次构建未完成请求则被忽略
- 指定镜像tag或Git的tag、分支等进行

# Webhooks

- HTTP call-back
- 镜像更新时通知用户
- Webhook chains

# Webhook chains

- 逐条执行
- 前一hook验证（validate）之后，后一条才会执行
- hook链只有全部hook都完成验证，才认为是完成。
- 通过hook消息体中的callback_url进行POST验证
- 验证消息内容
- state：必选参数success/failure/error
- description：显示在Docker Hub的详细信息，255字符
- context：操作场景信息，100字符以内
- target_url：用来查看hook执行结果的网址

# 组织（团队）

- Organizations and teams
- Owners team具有创建team、仓库和自动构建的权限
- 协作者 Collaborators
- 读取私有仓库
- 向非自动构建仓库push
- Admin权限，修改仓库的描述，写作者；公开、私有以及删除仓库

# 课后作业

- 注册GitHub/Bitbucket和Docker Hub
- 创建自动构建
- 提交Dockerfile进行自动构建
- 本地docker pull下载该自动构建镜像
