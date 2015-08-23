FROM centos:7 

MAINTAINER bin liu <liubin0329@gmail.com>

RUN yum update -y

# 安装Java和Git
RUN yum install -y java-1.8.0-openjdk git

ENV JENKINS_HOME /opt/jenkins/data
ENV JENKINS_MIRROR http://mirrors.jenkins-ci.org

# 下载Jenkins的war包
RUN mkdir -p $JENKINS_HOME/plugins
RUN curl -sf -o /opt/jenkins/jenkins.war -L $JENKINS_MIRROR/war-stable/latest/jenkins.war

# 安装Jenkins插件
RUN for plugin in chucknorris greenballs scm-api git-client git ws-cleanup ;\
    do curl -sf -o $JENKINS_HOME/plugins/${plugin}.hpi \
       -L $JENKINS_MIRROR/plugins/${plugin}/latest/${plugin}.hpi ; done

EXPOSE 8080

# 添加启动命令
CMD ["java", "-jar", "/opt/jenkins/jenkins.war"]
