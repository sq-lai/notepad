

# 重点：docker设置开机自启，创建的容器创建的时候也要加命令

```
--restart always
```

不然每次docker ps会发现docker可以运行，他里面的容器运行不了。例如：

```
#创建⽹络
docker network create blog
#启动mysql
docker run -d -p 3306:3306 \
-e MYSQL_ROOT_PASSWORD=123456 \
-e MYSQL_DATABASE=wordpress \
-v mysql-data:/var/lib/mysql \
-v /app/myconf:/etc/mysql/conf.d \
--restart always --name mysql \
--network blog \
mysql:8.0
#启动wordpress
docker run -d -p 8080:80 \
-e WORDPRESS_DB_HOST=mysql \
-e WORDPRESS_DB_USER=root \
-e WORDPRESS_DB_PASSWORD=123456 \
-e WORDPRESS_DB_NAME=wordpress \
-v wordpress:/var/www/html \
--restart always --name wordpress-app \
--network blog \
wordpress:latest
```

上述有一个博客系统，两个做好。**访问博客网站就有，同时网站定义的数据还可以自动保存到数据库**

可以作为自己的装逼hhh！

**快捷一键删除所有容器**

```bash
docker rm -f $(docker ps -aq)   //q代表只展示id
```

补充：

**yum和apt-get都是包管理工具**

**centos自带yum,ubuntu自带了apt-get**

# 一、docker定义

定义：Docker 是一个开源的应用**容器**引擎，它允许开发者打包他们的**应用以及应用的依赖包**到一个可移植的容器中，然后**发布到任何流行的 Linux 机器上**，也可以实现虚拟化。容器是完全使用**沙箱机制**，**相互之间不会有任何接口。**

小白理解：想象一下，你有一个应用程序，这个应用程序在你的电脑上运行得很好。但是，当你把这个应用程序分享给你的朋友，或者想要在其他电脑上运行它时，你发现它不能正常工作了。这可能是因为你的应用程序需要一些特定的软件和设置，而这些在其他电脑上可能没有。
**Docker 就像是应用程序的“行李箱”。**你可以把应用程序和它需要的所有东西（**比如特定的软件、设置等**）都打包进这个“行李箱”里。这样，**不管你把“行李箱”带到哪个电脑上，应用程序都能像在你电脑上一样正常运行，因为“行李箱”里包含了它需要的一切。**

# 二、Docker 的主要作用：

1.  **方便移植**：就像前面说的，Docker 可以帮助你把应用程序和它需要的环境打包在一起，这样应用程序就可以在任何地方运行，而不用担心环境问题。
2.  **节省资源**：Docker 用的是一种轻量级的**“虚拟化”技术**。它不像传统的虚拟机那样需要模拟整个操作系统，**而是直接运行在宿主机的操作系统上。**这样，Docker 容器启动更快，占用的资源也更少。
3.  **隔离环境**：每个 Docker 容器都是独立的，它们之间不会互相影响。这意味着你可以在一个电脑上运行多个容器，**每个容器都有自己的应用程序和环境，而不会互相干扰。**
4.  易于管理：Docker 提供了很多命令，让你可以很容易地管理容器**，比如启动、停止、删除容器，或者查看容器的状态等。**

# 三、docker简单使用

•  拉取镜像：Docker 容器是**基于“镜像”运行的**。你可以想象镜像就像是容器的“模板”。你可以从 Docker 的“仓库”（**一个在线的镜像库：docker hub**）中拉取你需要的镜像。
•  运行容器：**一旦你有了镜像，你就可以用它来创建并运行容器。**这就像是用“模板”来创建一个应用程序的实例。
•  管理容器：你可以很容易地管理你的容器，比如查看正在运行的容器，或者停止、删除容器等。

简而言之，**Docker 镜像就像是应用程序的“配方”**，它定义了如何构建和运行应用程序，**而容器则是根据这个“配方”制作出来的“成品”。**通过使用 Docker 镜像，你可以确保应用程序在任何环境中都能以相同的方式运行。

**重点1：Docker 容器设计的初衷是“一个容器一个应用程序”。这种设计哲学被称为“单职责原则”，意味着每个容器应该只运行一个应用程序或者服务。**

**重点2：docker非常适合微服务架构，每个服务可以独立运行在容器中。**

![image-20241015154427193](C:\Users\14586\AppData\Roaming\Typora\typora-user-images\image-20241015154427193.png)

# 四、常用命令看另一份PDF

# **补充1：仓库和镜像源**

仓库就是存放镜像的地方，**仓库分为公有仓库和私有仓库。**

Docker 公司提供了公共的镜像仓库 `https://hub.docker.com`，里面提供了大量的镜像可以给我们给我们使用，我们也可以基于别人的镜像来创建我们自己的镜像。**但是国内访问 `dockerhub` 速度比较慢，一般使用阿里云或网易云。**

在使用 `docker run` 运行镜像的时候，docker 会检查本机是否存在镜像，如果存在就使用这个镜像运行为一个容器，而如果不存在，就会去 Docker Hub上下载，下载完成，再运行这个镜像。

![image-20241015171114228](C:\Users\14586\AppData\Roaming\Typora\typora-user-images\image-20241015171114228.png)

镜像源可以用一下命令更改，**镜像加速就是国外的dockerhub访问满了，用国内镜像站点**

```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
"registry-mirrors": [
"https://do.nark.eu.org",
"https://dc.j8.work",
"https://docker.m.daocloud.io",
"https://dockerproxy.com",
"https://docker.mirrors.ustc.edu.cn",
"https://docker.nju.edu.cn"
]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

自己的云上之家，使用单独的阿里云镜像加速！！！**不行这个！！！！！！！！**

```bash
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://mj4rvjxn.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```



# **补充2：仓库的区别**

```
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

yum类似于conda

**这里的仓库设置相当于是设置后续下载安装docker去哪里安装下载哪个操作系统的**

上面的是镜像的仓库！！！！





# **补充3：记住docker相当于就是里面有一个小型的linus系统，有自己的文件系统**

nginx的端口号**默认是80，除非修改**

云服务器安全组里面设置了哪些主机端口放开，**端口映射的时候需要用开放的，不然自己定义新的**

**进行容器相当于进入小型的linus系统里面**



# **补充4：docker-hub的登录用户名密码**

1.siqilai  **用户名相当于把下划线自动取消了**

2.Lsq201314

**上传的话被禁止了！！！**



# **补充5：目录挂载和卷映射**

目录挂载：

相当于把容器内部的目录与外部主机的目录形成联系，

1.可以在外部主机的目录修改，**展示也会以外部主机目录为准，外部主机没有就不展示**

2.容器误删也不影响，**在创建后加速目录挂载命令即可。**

卷映射：

代码 -v 卷名：/xxx/xxx

**前面写卷名其他的不变**

卷挂载是以内部的配置文件为主。

# 补充6：docker创建后会有自己的ip

![image-20241016141941656](C:\Users\14586\AppData\Roaming\Typora\typora-user-images\image-20241016141941656.png)

容器创建，**docker0会分配ip，但不支持主机域名**

容器之间相互访问，进入容器后，**访问容器自己的ip+容器端口号**

创建自定义网络，这样容器名就是域名（类似www.baidu.com）不用关心ip是什么了

之后进入容器内部，实现

```bash
docker network create mynet
docker run -d -p 80:80 --name app1 --network mynet nginx 
docker run -d -p 90:80 --name app2 --network mynet nginx 
docker exec -it app1 bash
curl http://app2:80  //容器之间内部访问端口号是容器端口！！！！
```

# 补充7：redis主从同步集群

![image-20241016194916073](C:\Users\14586\AppData\Roaming\Typora\typora-user-images\image-20241016194916073.png)

redis主机写数据，从机读数据。主机改变，从机自动改变！**实现读写分离！！！**

**镜像使用bitnami/redis，**它的主从同步集群可以通过**设置环境变量实现**，更简单！

都用了目录挂载：在配置环境变量的时候，会要写入主机的文件！可能没权限：输入

chmod -R 777 xxxx    (**给这个文件所有权限**)，chmod权限修改，-R递归，代表多级目录一直来给权限；777代表所欲权限

docker logs id 可以查看日志信息

# 补充8：mysql的注意事项

```console
/etc/mysql/conf.d  //这个是mysql的配置信息目录
```

```
/var/lib/mysql  //这个是mysql的数据目录
```

上述两个目录可以通过目录映射到外部主机，**一般放在/app下的文件夹**

![image-20241016204436493](C:\Users\14586\AppData\Roaming\Typora\typora-user-images\image-20241016204436493.png)

有一个概念明白，目录挂载以外面的为主**。-e配置的信息会直接过去，没有的就是权限问题！！！**

# 补充9：compose一键启动多个容器，不需要每个手动敲

编写compose.yaml

![image-20241016210124167](C:\Users\14586\AppData\Roaming\Typora\typora-user-images\image-20241016210124167.png)

要包含顶级元素前四个里面的内容！！！

name:自己取，可以是某个功能

services:要启动的容器应用

nerworks:配置网络

volumes：目录挂载；**如果用了卷映射，还要单独写个volumes**

方法：**去docker的reference里面查看顶级元素不同内容的写法！！！方法很重要！！！**

在Linux系统中，**Vim**（Vi IMproved）是一款强大的文本编辑器，**输入vim 文件名即可编辑**

```
docker compose up -d   //后台运行启动compose.yaml
docker compose down   //关闭所有它启动的程序容器，但卷映射的卷不会消失。
docker compose down --rmi all --v  //移除镜像和卷
```

# 补充10：Dockerfile制作镜像

![image-20241017105816774](C:\Users\14586\AppData\Roaming\Typora\typora-user-images\image-20241017105816774.png)

**常用的，基础的就是蓝色框框的**

1.本地上传服务器使用命令：**rz**（**没有的还自己下载，centos用yum,ubuntu用apt-get**）

服务器下载是sz

2.java运行jar包

```java
java -jar xxx.jar
```

3.dockerfile完成后，build构建

```
docker build -f dockerfile（dockerfile的文件名） -t myapp:v1.0（自己镜像的名字） .  //最后的.不能少代表当前目录
```

# 补充11：镜像分层机制

重点：**不要觉得镜像下一堆会很占用存储空间，镜像分层，相同的一起占用，只是不同的再去开辟！！！**

Docker镜像分层的概念就像是**把每一步的操作都记录成一层**，比如安装系统、添加文件、配置软件等等。**每一层都是前一层的“增量更新”。这些层加在一起，就形成了完整的镜像。**

Docker使用分层的好处有很多，简单说就是“节省空间”和“加快速度”：

- **节省空间**：每一层都是独立的，而且可以重复利用。例如，如果你有两个镜像，它们都是基于相同的基础层（比如Ubuntu），那么这部分只需要保存一次，不需要为每个镜像都重复存储一遍，节省了磁盘空间。
- **加快构建和传输**：分层让Docker构建镜像时更高效。比如当你修改了一小部分代码，Docker只需要重新构建包含修改的那一层，其他没有变的层可以直接复用。这也意味着在上传或下载镜像时，只有修改的部分需要传输，大大减少了时间。

**总结精华：**

- Docker镜像是由**许多“层”叠加组成**的，像一摞积木块。
- 每一层都是独立的，**只有新增或者修改的部分才会增加新的层。**
- 分层的好处是**节省空间和加快镜像构建与传输的速度。**
- **镜像**的每一层是**只读**的，**容器**运行时会添加一个**可写层**。

























