# Lesson-13 Docker CaaS

# 网址

- 国内线路： http://www.alauda.cn/
- 国际线路： http://www.alauda.io/

# 安装命令行工具

## 安装pip

```
wget https://bootstrap.pypa.io/get-pip.py
sudo python get-pip.py
```

## 安装alauda命令行工具

```
sudo yum install -y python-devel libffi-devel openssl-devel
sudo pip install alauda
```
在执行任何命令的时候，如果出现警告：

```
InsecurePlatformWarning: A true SSLContext object is not available. This prevents urllib3 from configuring SSL appropriately and may cause certain SSL connections to fail. For more information, see https://urllib3.readthedocs.org/en/latest/security.html#insecureplatformwarning.
```
可以尝试:

```
sudo pip install pyopenssl ndg-httpsclient pyasn1
```

来解决这个问题。

# 实验


## 基本命令

```
$ alauda login
$ alauda service inspect blog
$ alauda ps


$ alauda stop db1
$ alauda service instances blog

$ alauda rm db1
$ alauda rm blog
```

## Alauda Compose

```
alauda compose up

alauda ps

```


## 备份数据卷

```
select * from wp_options limit 2;
```


# 参考链接

- Alauda主页

http://www.alauda.cn/

- Alauda文档中心

http://docs.alauda.cn/

- Alauda命令行

http://docs.alauda.cn/