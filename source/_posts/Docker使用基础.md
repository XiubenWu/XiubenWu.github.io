---
title: Docker使用基础
excerpt: -----
photos:
  -	https://gitlab.com/XiubenWu/xiubenwu-images/-/raw/master/img/20210812Docker0.png
date: 2021-08-12 21:04:11

tags:
  -	Docker
categories:
  -	笔记

---



# Docker的安装

官方脚本自动安装：

```
curl -fsSL https://get.docker.com | bash -s docker --mirror aliyun
```

国内daocloud自动安装:

```
curl -sSL https://get.daocloud.io/docker | sh
```

若存在旧版本docker,先将旧版本卸载：

```
$ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

## Docker镜像仓库

默认的DockerHub仓库在国外，从国内拉取速度较慢，国内许多云服务商提供了镜像加速服务。以阿里云为例，前往[容器镜像服务网站](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors):

![image-20210812133532470](https://gitlab.com/XiubenWu/xiubenwu-images/-/raw/master/img/20210812Docker1.png)

获取自己的加速地址并按照操作文档中的步骤进行配置。



# Docker容器使用

直接查看所有Docker命令：

```
$ docker
```

## 镜像操作

- 拉取镜像：

```
$ docker pull [images name]
```

当我们在本地主机上使用（run）一个不存在的镜像时 Docker 就会自动下载这个镜像。如果我们想预先下载这个镜像，我们可以使用 docker pull 命令来下载它

- 列出docker中的所有镜像：

```
$ docker images
```

同一仓库源可以有多个 TAG，代表这个仓库源的不同个版本我们使用 REPOSITORY:TAG 来定义不同的镜像。如果你不指定一个镜像的版本标签docker 将默认使用 REPOSITORY:latest​ 镜像。

- 搜索镜像：

```
$  docker search [images name]
```

或者前往[官方网站](https://hub.docker.com/)查找镜像.

- 删除镜像：

```
$ docker rmi [images name]
```

- 创建镜像：

创建镜像常用的两种方式为：

1. 从已经创建的容器中更新镜像，并且提交这个镜像
2. 使用 Dockerfile 指令来创建一个新的镜像

> 更新镜像：
>
> 更新镜像之前先使用镜像创建一个容器：
>
> ```
> $ docker run -it ubuntu:15.10 /bin/bash
> ```
>
> 在运行的容器内使用 `apt-get update `命令进行更新。
>
> 操作完成后使用`exit`退出容器
>
> 使用`docker commit`来提交容器副本
>
> ```
> $ docker commit -m="test" -a="wxb" 4758145e9199 wxb/ubuntu:test
> ```
>
> - **-m:** 提交的描述信息
> - **-a:** 指定镜像作者
> - **4758145e9199：**容器 ID
> - **wxb/ubuntu:test:** 指定要创建的目标镜像名



>构建镜像：
>
>使用`docker build`从零开始构建一个新的镜像。首先需要创建Dockerfile文件（文件中包含一组构建镜像的指令）：
>
>```
>$ cat > Dockerfile (vim Dockerfile)
>FROM    ubuntu:15.10
>MAINTAINER      Fisher "test@test.com"
>
>RUN     /bin/echo 'root:123456' |chpasswd
>RUN     useradd wxb
>RUN     /bin/echo 'wxb:123456' |chpasswd
>RUN     /bin/echo -e "LANG=\"en_US.UTF-8\"" >/etc/default/local
>EXPOSE  22
>EXPOSE  80
>CMD     /usr/sbin/sshd -D
>```
>
>从Dockerfile文件构建镜像：
>
>```
>docker build -t ubuntu:test ./
>```
>
>- **-t** ：指定要创建的目标镜像名
>- **./** ：Dockerfile 文件所在目录，可以指定Dockerfile 的绝对路径

- 添加新的镜像标签

可以使用 docker tag 命令，为镜像添加一个新的标签：

```
$ docker tag [images id] ubuntu:newtag
```

此时镜像id不变，只是多了一个指向它的标签。



***



## 容器操作

- 创建并启动容器：

```
$ docker run -it ubuntu /bin/bash
```

或者：

```
$ docker run -i -t ubuntu /bin/bash
```

**-i**: 交互式操作;**-t**: 终端。另外**-d**表示容器在后台运行，使用**-d**参数后不会自动进入容器，需要手动进入，可以使用**attach**和**exec**命令，一般使用exec**命令**，退出容器后不会自动停止容器(**--name**参数可以命名容器)。

```
$ docker exec -it [id|name] /bin/bash
```

- 启动、停止、重启容器：

```
$ docker start|stop|restart [container id|name]
stop容器时跟上-t参数代表延时多少时间停止(默认为10)
```

- 导出和导入容器：

```
$ docker export [container id] > name.tar
```

```
$ cat path/name.tar | docker import - test/ubuntu:v1
```

从url地址导入容器：

```
$ docker import http://example.com/exampleimage.tgz example/imagerepo
```

- 删除容器：

```
$ docker rm -f [container id]
```

一次删除不在运行状态的所有容器：

```
$ docker container prune
```

- 批量删除容器：

```
$ docker ps -a # 查看运行中容器状态
$ docker ps -a # 查看所用容器状态
$ docker ps -aq # 列出所有容器的id
$ docker stop $(docker ps -aq) # 批量停止所用的容器
$ docker rm $(docker ps -aq) # 批量停止所用的容器
```





## 容器端口映射

自动随机配置主机端口至容器开放端口：

```
$ docker run -d -P [images Name] [Commend]
```

自定义主机端口：

```
$ docker run -d -p host_port [images Name] [Commend]
```

或者：

```
$ docker run -d -p host_port:container_port [images Name] [Commend]
```

指定主机IP地址：

```
$ docker run -d -p 127.0.0.1:host_port:container_port [images Name] [Commend]
```

默认为tcp端口，可指定udp端口：

```
$ docker run -d -p 127.0.0.1:host_port:container_port/udp [images Name] [Commend]
```

查看端口绑定情况：

```
$ docker port [container id|name] container_port
```



## Docker容器互联

docker除了通过端口映射连接外，还可以通过连接系统将多个容器连接到一起，共享连接信息。

- 新建Docker网络：

```
$ docker network create -d bridge test-net
```

**-d**：参数指定 Docker 网络类型，有 bridge、overlay。

- 查看docker网络

```
$ docker network ls
```

- 容器连接到网络(**--network参数**)：

```
$ docker run -itd --name test1 --network test-net ubuntu /bin/bash
```

- 配置容器DNS:

在宿主机的 /etc/docker/daemon.json 文件中增加以下内容来设置全部容器的 DNS（重启docker后生效）：

```
{
  "dns" : [
    "114.114.114.114",
    "8.8.8.8"
  ]
}
```

查看容器的DNS信息：

```
$ docker run -it --rm  ubuntu  cat etc/resolv.conf
```

手动配置容器DNS信息：

```
$ docker run -it --rm -h host_ubuntu  --dns=114.114.114.114 --dns-search=test.com ubuntu
```

**--rm**：容器退出时自动清理容器内部的文件系统。

**-h HOSTNAME 或者 --hostname=HOSTNAME**： 设定容器的主机名，它会被写到容器内的 /etc/hostname 和 /etc/hosts。

**--dns=IP_ADDRESS**： 添加 DNS 服务器到容器的 /etc/resolv.conf 中，让容器用这个服务器来解析所有不在 /etc/hosts 中的主机名。

**--dns-search=DOMAIN**： 设定容器的搜索域，当设定搜索域为 .example.com 时，在搜索一个名为 host 的主机时，DNS 不仅搜索 host，还会搜索 host.example.com



***



# Docker 仓库管理

目前 Docker 官方维护了一个公共仓库 [Docker Hub](https://hub.docker.com/)。大部分需求都可以通过在 Docker Hub 中直接下载镜像来实现，其他运营商也提供相应的Docker仓库。

- 登录，登出自己的docker账号，登录后可以拉取自己账号下的镜像：

```
$ docker login
```

```
$ docker logout
```

- 登录后将自己的镜像推送到Docker Hub:

```
$ docker tag ubuntu:15.10 username/ubuntu:15.10
$ docker push username/ubuntu:15.10
```



***



# 其他Docker部件

## Docker Dockerfile

Dockerfile 是一个用来构建镜像的文本文件，文本内容包含了一条条构建镜像所需的指令和说明。

- **FROM 和 RUN 指令**

**FROM**：定制的镜像都是基于 FROM 的镜像.

**RUN**：用于执行后面跟着的命令行命令。有shell格式和exec格式

```
RUN <命令行命令>
# <命令行命令> 等同于，在终端操作的 shell 命令。
```

```
RUN ["可执行文件", "参数1", "参数2"]
# 例如：
# RUN ["./test.php", "dev", "offline"] 等价于 RUN ./test.php dev offline
```

**注意**：Dockerfile 的指令每执行一次都会在 docker 上新建一层。所以过多无意义的层，会造成镜像膨胀过大。

```
FROM centos
RUN yum install wget
RUN wget -O redis.tar.gz "http://download.redis.io/releases/redis-5.0.3.tar.gz"
RUN tar -xvf redis.tar.gz
以上执行会创建 3 层镜像。可简化为以下格式：
FROM centos
RUN yum install wget \
    && wget -O redis.tar.gz "http://download.redis.io/releases/redis-5.0.3.tar.gz" \
    && tar -xvf redis.tar.gz
```

- 构建镜像

```
$ docker build -t ubuntu:test .
```

此命令中的最后一个指令`.`为上下文路径，是指 docker 在构建镜像，有时候想要使用到本机的文件（比如复制），docker build 命令得知这个路径后，会将路径下的所有内容打包。

由于 docker 的运行模式是 C/S。我们本机是 C，docker 引擎是 S。实际的构建过程是在 docker 引擎下完成的，所以这个时候无法用到我们本机的文件。这就需要把我们本机的指定目录下的文件一起打包提供给 docker 引擎使用。因此Dockerfile文件路径下不要放过多的无关文件，否则会一起打包给容器。

如果未说明最后一个参数，那么默认上下文路径就是 Dockerfile 所在的位置。

- 其他指令：

> COPY:复制指令，从上下文目录中复制文件或者目录到容器里指定路径。
>
> ADD:ADD 指令和 COPY 的使用格类似(同样需求下，官方推荐使用 COPY).
>
> CMD:类似于 RUN 指令，用于运行程序，但二者运行的时间点不同,CMD 在docker run 时运行,RUN 是在 docker build时运行。
>
> ENTRYPOINT：类似于 CMD 指令，但其不会被 docker run 的命令行参数指定的指令所覆盖，而且这些命令行参数会被当作参数送给 ENTRYPOINT 指令指定的程序。
>
> ENV:设置环境变量，定义了环境变量，那么在后续的指令中，就可以使用这个环境变量。
>
> ARG:构建参数，与 ENV 作用一致。不过作用域不一样。ARG 设置的环境变量仅对 Dockerfile 内有效，也就是说只有 docker build 的过程中有效，构建好的镜像内不存在此环境变量。
>
> VOLUME:定义匿名数据卷。在启动容器时忘记挂载数据卷，会自动挂载到匿名卷。
>
> EXPOSE:仅仅只是声明端口。作用：帮助镜像使用者理解这个镜像服务的守护端口，以方便配置映射。
> 在运行时使用随机端口映射时，也就是 docker run -P 时，会自动随机映射 EXPOSE 的端口。
>
> WORKDIR:指定工作目录。用 WORKDIR 指定的工作目录，会在构建镜像的每一层中都存在。
>
> USER:用于指定执行后续命令的用户和用户组，这边只是切换后续命令执行的用户（用户和用户组必须提前已经存在）。
>
> HEALTHCHECK:用于指定某个程序或者指令来监控 docker 容器服务的运行状态。
>
> ONBUILD:用于延迟构建命令的执行。简单的说，就是 Dockerfile 里用 ONBUILD 指定的命令，在本次构建镜像的过程中不会执行.
>
> LABEL:LABEL 指令用来给镜像添加一些元数据（metadata），以键值对的形式.



## Docker Compose

Compose 是用于定义和运行多容器 Docker 应用程序的工具。通过 Compose，您可以使用 YML 文件来配置应用程序需要的所有服务。然后，使用一个命令，就可以从 YML 文件配置中创建并启动所有服务。

- yml 配置指令参考

> version:指定本 yml 依从的 compose 哪个版本制定的。
>
> build:指定为构建镜像上下文路径：
>
> cap_add，cap_drop:添加或删除容器拥有的宿主机的内核功能。
>
> cgroup_parent:为容器指定父 cgroup 组，意味着将继承该组的资源限制。
>
> command:覆盖容器启动的默认命令。
>
> container_name:指定自定义容器名称，而不是生成的默认名称。
>
> depends_on:设置依赖关系。
>
> deploy:指定与服务的部署和运行有关的配置。只在 swarm 模式下才会有用。
>
> devices:指定设备映射列表。
>
> dns:自定义 DNS 服务器，可以是单个值或列表的多个值。
>
> dns_search:自定义 DNS 搜索域。可以是单个值或列表。
>
> entrypoint:覆盖容器默认的 entrypoint。
>
> env_file:从文件添加环境变量。可以是单个值或列表的多个值。
>
> environment:添加环境变量。您可以使用数组或字典、任何布尔值，布尔值需要用引号引起来，以确保 YML 解析器不会将其转换为 True 或 False。
>
> expose:暴露端口，但不映射到宿主机，只被连接的服务访问。
>
> extra_hosts:添加主机名映射。类似 docker client --add-host。
>
> healthcheck:用于检测 docker 服务是否健康运行。
>
> image:指定容器运行的镜像。以下格式都可以.
>
> ```
> image: redis
> image: ubuntu:14.04
> image: tutum/influxdb
> image: example-registry.com:4000/postgresql
> image: a4bc65fd # 镜像id
> ```
>
> logging:服务的日志记录配置。
>
> network_mode:设置网络模式。
>
> restart:
>
> ```
> restart: "no" 				是默认的重启策略，在任何情况下都不会重启容器。
> restart: always				容器总是重新启动。
> restart: on-failure			在容器非正常退出时（退出状态非0），才会重启容器。
> restart: unless-stopped		在容器退出时总是重启容器，但是不考虑在Docker守护进程启动时就已经停止了的容器
> ```
>
> secrets:存储敏感数据，例如密码.
>
> security_opt:修改容器默认的 schema 标签。
>
> stop_grace_period:指定在容器无法处理 SIGTERM (或者任何 stop_signal 的信号)，等待多久后发送 SIGKILL 信号关闭容器。
>
> stop_signal:设置停止容器的替代信号。默认情况下使用 SIGTERM 。
>
> sysctls:设置容器中的内核参数，可以使用数组或字典格式。
>
> tmpfs:在容器内安装一个临时文件系统。可以是单个值或列表的多个值。
>
> ulimits:覆盖容器默认的 ulimit。
>
> volumes:将主机的数据卷或着文件挂载到容器里。



## Docker Machine

Docker Machine 是一种可以让您在虚拟主机上安装 Docker 的工具，并可以使用 docker-machine 命令来管理主机。

Docker Machine 管理的虚拟主机可以是本地主机上的，也可以是云供应商，如阿里云，腾讯云，AWS，或 DigitalOcean。

使用 docker-machine 命令，您可以启动，检查，停止和重新启动托管主机，也可以升级 Docker 客户端和守护程序，以及配置 Docker 客户端与您的主机进行通信。

- 安装Docker Machine:

安装 Docker Machine 之前你需要先安装 Docker。

```
Linux安装
$ base=https://github.com/docker/machine/releases/download/v0.16.0 &&
  curl -L $base/docker-machine-$(uname -s)-$(uname -m) >/tmp/docker-machine &&
  sudo mv /tmp/docker-machine /usr/local/bin/docker-machine &&
  chmod +x /usr/local/bin/docker-machine
```

- 基本命令：

```
$ docker-machine ls 列出可用的机器
```

```
$ docker-machine create --driver virtualbox test 创建一台名为 test 的机器。
```

```
$ docker-machine ip test 查看机器的 ip
```

```
$ docker-machine stop test 停止机器
```

```
$ docker-machine start test 启动机器
```

```
$ docker-machine ssh test 进入机器
```

- 命令参数说明：

>- **config**：查看当前激活状态 Docker 主机的连接信息。
>- **create**：创建 Docker 主机
>- **env**：显示连接到某个主机需要的环境变量
>- **inspect**： 以 json 格式输出指定Docker的详细信息
>- **ip**： 获取指定 Docker 主机的地址
>- **kill**： 直接杀死指定的 Docker 主机
>- **ls**： 列出所有的管理主机
>- **provision**： 重新配置指定主机
>- **regenerate-certs**： 为某个主机重新生成 TLS 信息
>- **restart**： 重启指定的主机
>- **rm**： 删除某台 Docker 主机，对应的虚拟机也会被删除
>- **ssh**： 通过 SSH 连接到主机上，执行命令
>- **scp**： 在 Docker 主机之间以及 Docker 主机和本地主机之间通过 scp 远程复制数据
>- **mount**： 使用 SSHFS 从计算机装载或卸载目录
>- **start**： 启动一个指定的 Docker 主机，如果对象是个虚拟机，该虚拟机将被启动
>- **status**： 获取指定 Docker 主机的状态(包括：Running、Paused、Saved、Stopped、Stopping、Starting、Error)等
>- **stop**： 停止一个指定的 Docker 主机
>- **upgrade**： 将一个指定主机的 Docker 版本更新为最新
>- **url**： 获取指定 Docker 主机的监听 URL
>- **version**： 显示 Docker Machine 的版本或者主机 Docker 版本
>- **help**： 显示帮助信息



## Swarm 集群管理

Docker Swarm 是 Docker 的集群管理工具。它将 Docker 主机池转变为单个虚拟 Docker 主机。 Docker Swarm 提供了标准的 Docker API，所有任何已经与 Docker 守护程序通信的工具都可以使用 Swarm 轻松地扩展到多个主机。

```
$ docker-machine create -d virtualbox swarm-manager # 创建 swarm 集群管理节点（manager）
```

初始化 swarm 集群，进行初始化的这台机器，就是集群的管理节点。

```
$ docker-machine ssh swarm-manager
$ docker swarm init --advertise-addr 192.168.99.107 #这里的 IP 为创建机器时分配的 ip。
```

```
$ docker info 查看集群信息
```

```
docker@swarm-manager:~$ docker service create --replicas 1 --name helloworld alpine ping docker.com 部署服务到集群中
```

```
docker@swarm-manager:~$ docker service ps helloworld 查看服务部署情况
```

```
docker@swarm-manager:~$ docker service scale helloworld=2 扩展集群服务
```

```
docker@swarm-manager:~$ docker service rm helloworld 删除服务
```

```
docker@swarm-manager:~$ docker service create --replicas 1 --name redis --update-delay 10s redis:3.0.6
docker@swarm-manager:~$ docker service update --image redis:3.0.7 redis 滚动升级服务
```

```
docker@swarm-manager:~$ docker node ls 
docker@swarm-manager:~$  docker node update --availability active swarm-worker1 停止某个节点接收新的任务
```
