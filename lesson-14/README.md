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

- Source Code Management 中选git，输入 https://github.com/liubin/avatar_store ，涉及到认证关系，这里不能输入git://github.com。。。

- 下面的Build - build step中，选择Execute shell，内容如下:

```
IMAGE=$(docker build . | tail -1 | awk '{ print $NF }')

# Execute the build inside Docker.
CONTAINER=$(docker run -d -v "$WORKSPACE:/opt/project" $IMAGE /bin/bash -c "cd /opt/project && rake spec")

# Get its exit code as soon as the container stops.
RC=$(docker wait $CONTAINER)

# Delete the container we've just used.
docker rm $CONTAINER

docker rmi -f $IMAGE

# Exit with the same value as that with which the process exited.
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





