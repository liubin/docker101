FROM centos:7 

MAINTAINER bin liu <liubin0329@gmail.com>


# 单独添加一个文件
ADD files/file1.txt /dir1
ADD files/file2.txt /dir2/

# 添加多个文件
# Step 4 : ADD files/* /dir3
# When using ADD with more than one source file, the destination must be a directory and end with a /
# ADD files/* /dir3

ADD files/* /dir4/


# src为文件夹
ADD files /dir5
ADD files /dir6/
ADD files/ /dir7/

# 添加URL文件（带文件名）
ADD https://raw.githubusercontent.com/docker/docker/master/Dockerfile /url1

ADD https://raw.githubusercontent.com/docker/docker/master/Dockerfile /url2/


# 添加URL文件（不带文件名）
ADD https://github.com/ /url3

# Step 8 : ADD https://github.com/ /url4/
# Downloading 17.63 kB
# cannot determine filename from url: https://github.com/

# ADD https://github.com/ /url4/



