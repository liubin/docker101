# Docker入门与实践

Lesson-14 基于Docker进行CI（持续集成）
刘斌@Alauda
2015年8月

# 本节概要

- 什么是CI和Jenkins
- 普通CI的流程
- 如何使用Docker + Jenkins进行CI

# 测试需求

- 频繁进行
- 干净环境
- 打包安装费时
- 环境复杂
- 自动化
- 持续交付

# 什么是CI（Continuous Integration）

- 持续集成
- 早集成，常集成

# 在CI中使用Docker的优势

- 轻量快速
- 可复用（Dockerfile，镜像）
- 隔离（干净环境）

# Jenkins简介

- 开源CI服务
- SCM/部署
- plugin
- war文件发布，不需要数据库
- Web界面配置
- email通知
- 生成JUnit/TestNG报告
- 分布式构建支持

![](./images/jenkins_logo.png)

# Jenkins + Docker 模式

- Git hook -> Jenkins -> Build Docker 镜像-> 测试
- 不仅仅是效率的提升，更是一种变革
- DevOps紧密结合

# Jenkins + Docker

- Jenkins和测试都跑在容器中
- Docker in Docker

# Jenkins + Docker

```bash
a=$(sudo docker run -d --net=host -v /var/run/docker.sock:/var/run/docker.sock -v /usr/bin/docker:/usr/bin/docker -v /sys:/sys -v /lib64:/lib64 -p 8080:8080 j4)
```

# 实验

- 在Docker中使用Jenkins进行测试

# 课后作业

- 运行本示例代码

