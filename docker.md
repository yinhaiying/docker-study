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

3. ```javascript
   sudo mkdir -p /etc/docker
   sudo tee /etc/docker/daemon.json <<-'EOF'
   {
     "registry-mirrors": ["https://s360h258.mirror.aliyuncs.com"]
   }
   EOF
   sudo systemctl daemon-reload
   sudo systemctl restart docker
   ```

   需要一步一步地完成上面的四步操作。