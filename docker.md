

# Docker学习笔记



> **在容器化技术出现之前，大家是怎么做的？**

程序员加班加点提供一个应用程序 jar，由运维团队去部署。如果jar依赖于第三方中间件，比如redis、mysql、mq等。那运维也必须在自己的环境里面，安装上对应的依赖。伴随着系统业务的复杂程度，依赖的中间件的复杂度也再加强。这个过程，很让人痛苦，一不小心就会出错

## Docker安装



查看linux系统环境  cat /etc/os-release

![image-20210512160546580](C:\Users\zk\AppData\Roaming\Typora\typora-user-images\image-20210512160546580.png)



1、 删除旧版本

```shell
 sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

2 、安装docker,以CentOs为例

```shell
#1  yum install -y yum-utils
#2  yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
#3  yum-config-manager --enable docker-ce-nightly
#4 安装docker引擎
	yum install docker-ce docker-ce-cli containerd.io
#5 启动docker
	systemctl start docker
#6 测试是否安装成功
	docker version 出现docker描述信息即表明成功
#7 docker 卸载
yum remove docker-ce docker-ce-cli containerd.io
rm -rf /var/lib/docker
rm -rf /var/lib/containerd
```

3、配置阿里云镜像加速

找到

<img src="C:\Users\zk\AppData\Roaming\Typora\typora-user-images\image-20210513155610510.png" alt="image-20210513155610510" style="zoom:50%;" />



## docker的常用命令

### 帮助命令

```shell
docker version      # 显示docker的版本信息
docker info 	    # 显示dockers的系统信息，包括镜像和容器的数量
docker 命令 --help   # 帮助命令
```

### 镜像命令

**docker images** 查看本机上的所有镜像

````shell
[root@izbp1c7wd9bt3v83tqidp2z docker]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    d1165f221234   2 months ago   13.3kB

# 解释
REPOSITORY	镜像的仓库
TAG 		镜像标签
IMAGE ID	镜像的id
CREATED		创建时间
SIZE		大小

**构建镜像**
```shell
#1、编写Dockerfile文件
#2、再Dockerfile所在目录构建镜像：docker build -t nginx:v1 .
#3、通过镜像启动容器：docker run -it -P nginx:v1 /bin/bash
```




**docker search** 搜索镜像

**docker pull** 下载镜像

**docker rmi** 删除镜像

​```shell
 docker rmi -f 镜像id  #删除指定的镜像
 docker rmi -f 镜像id 镜像id 镜像id #删除多个镜像
 docker rmi -f $(docker images)   #删除所有镜像

```

### 容器命令

**前提：必须要有镜像，才可以创建容器**

**新建容器并启动**

``````shell
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

# 参数说明
--name="zk"  容器名字，用来区分容器
-d 			 以后台方式运行
-it			 使用交互方式
-p			指定容器的端口 -p 8080:8080
	-p ip:主机端口：容器端口
	-p 主机端口：容器端口（推荐）
	-p 容器端口
-P			大写的P随机指定端口

# 测试
docker run -it centos /bin/bash

``````

**列出所有运行的容器**

```shell
#docker ps
  #正在运行的容器
-a #所有包括正在运行和曾经运行过的
-n=? #显示最近运行过的n个容器
-q   #显示容器的编号
```

**退出容器**

```
exit	# 退出并停止
Ctrl+P+Q #容器不停止
```

**删除容器**

```shell
docker rm 容器id            	   #删除指定容器
docker rm -f $(docker ps -aq)   #删除所有容器
docker ps -a -q|xargs docker rm #删除所有容器
```

**启动和停止容器**

```shell
docker start 容器id	# 启动容器
docker restart 容器id # 重启容器
docker stop 容器id	# 停止容器
docker kill 容器id	# 停止容器
```

**常用的其他命令**



![image-20210513183007756](C:\Users\zk\AppData\Roaming\Typora\typora-user-images\image-20210513183007756.png)

**后台启动容器**

```shell
docker run -d centos  #启动后,发现centos停止了
#常见的坑：docker容器使用后台运行，就必须要有一个前台进程，docker发现没有应用，就会自动停止
```

**查看日志**

```shell
docker logs -f -t --tail 10 容器没有日志

# 自己编写一段shell脚本
docker run -d centos /bin/sh -c "while true;do echo zk leader;sleep 1;done"

# docker ps
CONTAINER ID   IMAGE       
fceefb41ede5   centos

# 显示日志
-tf				#
--tail number	#要显示日志条数

docker logs -tf --tail 10 fceefb41ede5
```



**查看容器中进程信息ps**

```
# docker top fceefb41ede5
UID                 PID                 PPID             
root                918                 886                
root                1752                918
```

**查看镜像的元数据**

```shell
# docker inspect 容器id
```



**进入当前正在运行的容器**

```shell
# docker exec -it 容器id /bin/bash

```



**从容器内拷贝文件到主机上**

```shell
# docker cp 容器id:path/file /targetPath
[root@izbp1c7wd9bt3v83tqidp2z zk]# docker cp fceefb41ede5:/a.zk /zk
[root@izbp1c7wd9bt3v83tqidp2z zk]# ls
a.zk  docker
```



> **docker 安装nginx**

```shell
1、 docker pull nginx  #拉取最新的镜像
2、 docker run -d -p 8088:80 nginx  #指定端口后台启动

```

































































