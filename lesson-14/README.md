# Lesson-14 基于Docker进行CI（持续集成）


# 构建Jenkins镜像


```
sudo docker build -t "jenkins" .

```

# 启动Jenkins

```
sudo mkdir -p /opt/jenkins/data/workspace

jk=$(sudo docker run -d --net=host -v /var/run/docker.sock:/var/run/docker.sock -v /usr/bin/docker:/usr/bin/docker -v /sys:/sys -v /lib64:/lib64 -v /var/lib/docker:/var/lib/docker -v /opt/jenkins/data/workspace:/opt/jenkins/data/workspace --privileged -p 8080:8080 jenkins)

sudo docker logs -f $jk
```

# 创建Jenkins任务

- 打开Jenkins http://localhost:8080

- 创建一个任务，选择Freestyle project，点击下一步

- Source Code Management 中选git，输入 https://github.com/liubin/docker101-sample-gem ，涉及到认证关系，这里不能输入git://github.com。。。

- 下面的Build - build step中，选择Execute shell，内容如下:

```
# 这里构建镜像，将镜像id保存到IMAGE变量中
IMAGE=$(docker build . | tail -1 | awk '{ print $NF }')

# 启动容器，容器ID放到CONTAINER中
CONTAINER=$(docker run -d -v "$WORKSPACE:/opt/project" $IMAGE /bin/bash -c "cd /opt/project && rake spec")

# 取得容器的返回值（正常退出为0）
RC=$(docker wait $CONTAINER)

# 测试结束后删除容器
docker rm $CONTAINER


# 镜像可根据情况选择是否删除
docker rmi -f $IMAGE

# 返回值传回给Jenkins
exit $RC

```

- Post-build Actions 中选择Publish JUnit test result report，输入 spec/reports/*.xml 。

- 点击左边的Build Now开始构建

- http://localhost:8080/job/test/1/console 查看console输出

# 对docker101-sample-gem进行测试

- https://github.com/liubin/docker101-sample-gem


# 删除Jenkins容器

```
sudo docker kill $jk
```


# 视频地址

* [基于Docker进行持续集成](http://www.kaikeba.com/kkb/kaikeba/content_video.html?vId=2749)
![](http://video.kk8.cdn.bj.xs3cnc.com/2c/i/covers/Docker30.jpg)



