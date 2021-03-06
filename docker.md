# docker

## docker的基本组成

![docker architecture](http://hainiubl.com/images/2016/architecture.jpg)

docker组成大致分为三个部分：客户端，服务器以及仓库。

客户端(client)用来启动docker中的容器(container)，而容器需要从仓库(registry)中进行下载。

#### 客户端(clilent):

客户端只要是运行一些docker命令，比如打包，拉取已经运行。

#### 服务器端(server)：

服务器端主要是保存docker镜像，通过镜像创建一个个的容器，每个容器就是一个或者一组应用，从而实现运行应用。

#### 镜像(image)：

镜像就好比是一个模板，可以通过这个模板来创建容器服务，比如一个tomcat镜像=>通过run运行起来 =>运行起来后就创建了一个具体的tomcat容器（比如tomcat01）,这个具体的容器就是用来提供服务的。通过这个镜像可以创建多个容器，最终服务或者项目的运行都是在容器中的。

#### 容器(container)：

docker利用容器技术，独立运行一个或者一组应用，容器都是通过镜像来创建的。容器具有启动，暂停，删除等基本命令。更加通俗地来说，我们可以把容器看做是一个简易的Linux系统，应用都是运行在系统上。

#### 仓库(repository)：

仓库就是用来存放镜像的地方。仓库分为公有仓库和私有仓库。

仓库默认都是国外的，因此我们通常需要配置镜像加速(比如阿里云，华为，网易的镜像加速)。

## 安装docker

#### 环境准备

1. 阿里云服务器
2. Linux基础

#### 安装

**安装**

```javascript
https://docs.docker.com/engine/install/ubuntu/     // ubuntu下安装
```

**查看是否安装成功**

```javascript
systemctl start docker    // 启动docker
docker version            // 如果出现版本信息表示安装成功
```

**查看docker能否正常运行**

```javascript
docker run hello-world    // 表示创建一个helloworld镜像
```

**查看hello-world镜像是否安装成功**

```javascript
docker images    // 查看所有镜像
REPOSITORY             TAG                 IMAGE ID            CREATED             SIZE
hello-world            latest              bf756fb1ae65        7 months ago        13.3kB
```

**卸载docker**

```javascript
apt-get purge docker-ce docker-ce-cli containerd.io    // Ubuntu  删除软件
rm -rf /var/lib/docker    // 删除目录    /var/lib/docker是docker的默认工作路径
```

**阿里云镜像加速**

1. 登录阿里云搜索容器镜像服务
2. 找到镜像中心的镜像服务器
3.  需要一步一步地完成下面的四步操作。

```javascript
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://s360h258.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

#### docker run 运行过程

docker run xxx会运行一个镜像，这个过程究竟是怎样的了？

![docker run 运行流程图.png](https://i.loli.net/2020/08/12/CSQnG5gkPtM3jdh.png)

从上面的过程中我们可以看到，当运行docker run hello-world时，docker会首先从本地进行查找，看本地是否已经存在hello-world镜像，如果本地有，那么就直接使用本地镜像进行运行。如果本地没有这个镜像，那么会从docker hub(类似于github)进行下载，如果docker hub存在该镜像，那么可以进行下载到本地并运行，如果docker hub没有该镜像，那么会报错，提示找不到该镜像。这就是整个的docker run 的过程。

#### docker的底层原理

**docker内部是如何工作的？**

docker是一个Client-Server的结构，Docker的守护进程运行在主机上，通过Socket从客户端访问。Docker-Server接收到Docker-Client的指令，守护进程就会执行这些命令。这些命令都是用于操作docker上的容器或者说镜像，所有的容器和镜像都是通过守护进程去进行操作。没有接收到lcient的命令，这些容器不会执行。

![docker是如何工作的.png](https://i.loli.net/2020/08/12/KjgnRN3YXduVC52.png)

**docker为什么比VM快？**

![Vmware-vs-Docker.jpg](https://i.loli.net/2020/08/12/5o2LVAK4GrEBeRu.jpg)

1. docker有着比虚拟机更少的抽象层。
2. docker利用的是宿主机的内核，而VM需要搭建一个新的Guest OS

所以说，新建一个容器的时候，docker不需要像虚拟机一样重新加载一个操作系统内核，而是直接使用宿主的内核。虚拟机是每创建一个容器都需要加载一个Guest OS。

## docker的常用命令

#### 帮助命令

```javascript
docker version       //  显示docker的版本信息
docker info          //  查看更详细的docker信息，包括镜像，容器信息等。
docker 命令 --help    //  帮助命令
```

官方文档地址

```javascript
https://docs.docker.com/reference/
```

#### 镜像命令

**docker images：**查看所有本地的主机上的镜像

```shell
root@haiying:~# docker images
REPOSITORY             TAG                 IMAGE ID            CREATED             SIZE
haiying/node-web-app   latest              51d94dc7db06        2 days ago          1.37GB
hello-world            latest              bf756fb1ae65        7 months ago        13.3kB
# 解释
REPOSITORY:镜像的仓库源名称
TAG:镜像的标签（版本信息）
IMAGE ID:镜像的id
CREATED:镜像创建的时间
SIZE:镜像的大小
# 可选项
root@haiying:~# docker images --help
Usage:  docker images [OPTIONS] [REPOSITORY[:TAG]]
List images
Options:
  -a, --all             # 列出所有的镜像
  -q, --quiet           # 只显示镜像的ID 
  
```

**docker search：**搜索镜像

```shell
root@haiying:~# docker search mysql
NAME       DESCRIPTION                                     STARS              OFFICIAL            
mysql      MySQL is a widely used, open-source relation…   9827                OK
```

**docker pull**：下载镜像

```shell
# docker pull + 镜像名称a:tag
docker pull mysql 
```

**docker rmi**：删除镜像

```shell
# docker rmi -f + 镜像id或者镜像名称  删除一个镜像
# docker images 通常删除前和删除后都使用docker images查看镜像是否存在或者是否删除成功
root@haiying:~# docker rmi -f hello-world
Untagged: hello-world:latest
Untagged: hello-world@sha256:49a1c8800c94df04e9658809b006fd8a686cab8028d33cfba2cc049724254202
Deleted: sha256:bf756fb1ae65adf866bd8c456593cd24beb6a0a061dedf42b26a993176745f6b
```



#### 容器命令

说明，我们有了镜像才能够创建容器，因此，我们可以先下载一个ubuntu来进行测试

```shell
docker pull ubuntu
root@haiying:~# docker pull ubuntu
Using default tag: latest
latest: Pulling from library/ubuntu
3ff22d22a855: Pull complete                                                                             e7cb79d19722: Pull complete                                                                             323d0d660b6a: Pull complete                                                                             b7f616834fd0: Pull complete                                                                             Digest: sha256:5d1d5407f353843ecf8b16524bc5565aa332e9e6a1297c73a92d3e754b8a636d
Status: Downloaded newer image for ubuntu:latest
docker.io/library/ubuntu:latest
```

**新建容器并启动**

```shell
docker run [potion] images  启动一个镜像
# 参数说明
--name=Name 容器名字  mysql1 mysql2 用来区分容器
-d          后台方式运行 （经常使用）
-it         进入容器,使用交互方式运行 （经常使用）
-p          指定容器端口         -p  8080:8080
    -p   主机端口：容器端口（最常用的）
    -p   容器端口
-P          随机指定端口

# 测试
root@haiying:~# docker run -it ubuntu /bin/bash        # 进入ubuntu容器中。
root@bcadb54c13dc:/# ls       #查看容器内的ubuntu 从获取的目录可以看到，实际上这个容器就是一个小型的Linux操作系统
# 容器内的操作系统和容器外的操作系统没有关系。
bin  boot  dev  etc  home  lib  lib32  lib64  libx32  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@bcadb54c13dc:/# 
# 从容器中退回主机
root@bcadb54c13dc:/# exit
exit
root@haiying:~#  
```

**列出所有运行容器的命令**

```shell
root@haiying:~# docker ps       # 列出当前正在运行的容器    
# 参数
        列出当前正在运行的容器
-a      列出所有运行过的容器(历史记录)
-n=2    列出最近运行的2个容器 

```

**退出容器**

```shell
exist      			# 退出容器同时停止容器
ctrl + P +Q 		# 不停止容器进行退出
```

**删除容器**

```shell
docker kill + 容器id  # 关闭正在运行的容器  
docker rm + 容器id    # docker rm不能删除正在运行的容器。如果想要强制删除使用rm -f
```

**启动和停止容器**

```shell
docker start + 容器id       # 启动一个容器
docker restart + 容器id     # 重启一个容器  通常我们可以通过docker ps -a 获取历史容器id，然后使用restart重启
docker stop + 容器id        # 停止一个容器
docker kill + 容器id        # 杀死一个容器
```

#### 常用的其他命令

**后台启动容器**

通常情况下我们都是通过docker run -it 的方式（即进入容器的方式进行启动），但是我们也可以直接通过后台启动容器，而不进入容器。

```shell
# docker run -d + 镜像名  来后台启动容器
root@haiying:~# docker run -d ubuntu
88e36488d4386915d59c2f90c55b767dee456e5a354665206caec16052949840
root@haiying:~# docker ps
# 问题：通过后台启动的方式，会发现docker ps查询时启动的容器被关闭了
# 常见的坑，docker 容器通过后台启动运行，那么就必须要有一个前台进程。docker发现没有应用，那么就会自动停止。
# 以nginx为例，容器启动后，发现自己没有提供服务，就会立刻停止。

```

**查看日志**

```shell
docker logs -tf --tail 10 + 容器ID      # 查看10条日志
# 参数说明
--tail 		要显示的条数
-tf			-t 表示时间戳  -f 表示输出日志
```

**查看容器中进程信息**

```shell
# docker top +容器ID 查看容器内进程信息

root@haiying:~# docker top 2c45
UID    PID     PPID     C    STIME     TTY  TIME             CMD
root   11347    11323    0    Aug09    ?    00:00:27       node /opt/yarn-v1.22.4/bin/yarn.js start
root   11399    11347   0     Aug09    ?    00:00:00       /bin/sh -c next start
```

**查看镜像的元数据**

```shell
# docker inspect + 容器ID  查看容器的元数据
root@haiying:~# docker inspect 2c45
[
    {
        "Id": "2c4526365f9577977a52e18b7e5e8a42c4864cbd1592641a45173655a80f5cfa",
        "Created": "2020-08-09T13:21:05.151305142Z",
        "Path": "docker-entrypoint.sh",
        "Args": [
            "yarn",
            "start"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 11347,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2020-08-09T13:21:05.53921525Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        # ...
        "Image": "sha256:51d94dc7db06d463c72019aec30c01b465008e516baf7b05fe7e192cb8e90f28",
        "Name": "/app",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "Config": {},
        "NetworkSettings": {
            "Networks": {
                "host": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "514dd57752f83059ae85f1a69d08cd236a05dad42f96f66d179d414f9c951342",
                    "EndpointID": "5b8e5ebde47973c0ab0d0980e3da8c6155d54f06aaeaf37e78b1c2b926d3a82c",
                    "Gateway": "",
                    "IPAddress": "",
                    "IPPrefixLen": 0,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "",
                    "DriverOpts": null
                }
            }
        }
    }
]
```

**进入当前正在运行的容器**

```shell
# 容器都是通过后台方式运行的，这时候如果我们需要修改一些容器配置，那么久需要进入容器。
# 命令
docker exec -it +容器ID + bash默认的命令行  # -it 以交互的方式进入

```

**从容器内拷贝数据**

数据都保存在docker容器中，只要容器没有被杀掉(无论容器是否处于运行开启状态)，那么数据就不会丢失。但是有时候为了防止数据丢失，我们需要把数据从容器内拷贝到服务器上。

```shell
# docker cp  容器ID：数据地址    服务器上地址
docker cp 23a4:/home/test /home
ps:这里的拷贝是一个手动的过程，实际上我们还可以通过-v 卷的技术可以实现自动同步
```

## 实践

#### 使用docker 部署一个nginx

```shell
1. docker search nginx     # 查找nginx  或者从docker hub中查找具体的详细
2. docker pull nginx       # 下载nginx
3. docker images           # 查看镜像是否下载成功
4. docker run -d --name nginx01  -p 3000:80 nginx   # 使用后台方式启动nginx 同时给nginx命名为nginx1 同时将服务器的3000端口映射到nginx的80端口，这样的话外界就可以通过3000端口来访问nginx了。
5. docker ps               # 查看nginx01容器是否启动
   #  或者  curl localhost:3000   或者
6. 阿里云服务器IP:3300  		# 在浏览器中访问，查看是否出现nginx的欢迎页面
7. docker exec -it nginx01 bash   # 进入nginx 如果想要进行配置信息的设置，必须进入容器
8. whereis nginx            # 查看nginx
```





