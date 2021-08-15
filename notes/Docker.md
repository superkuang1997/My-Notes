# docker🐳

Docker 是一个基于 Go 语言的开源的应用容器引擎，Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中， 然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。

容器是完全使用沙箱机制，相互之间不会有任何接口，更重要的是容器性能开销极低。

Docker 支持将软件编译成一个镜像，然后在镜像中各种软件做好配置，将镜像发布出去，其他使用者可以直接使用这个镜像。 运行中的这个镜像被称为容器。



## 参考文档

👉 [Docker 官方文档](https://docs.docker.com/)

👉 [中文文档  A](https://docker-doc.readthedocs.io/zh_CN/latest/installation/ubuntulinux.html)

👉 [中文文档  B](http://www.dockerinfo.net/document)



## 核心概念

- docker镜像（Images）：Docker 镜像是用于创建 Docker 容器的模板。 

- docker容器（Container）：容器是独立运行的一个或一组应用。 

- docker客户端（Client）：客户端通过命令行或者其他工具使用

- docker主机(Host)：一个物理或者虚拟的机器用于执行 Docker 守护进程和容器。 

- docker仓库(Registry)：Docker 仓库用来保存镜像，可以理解 为代码控制中的代码仓库。

  [Docker Hub](https://hub.docker.com) 提供了庞大的镜像集合供使用。



## 镜像加速

Perference -> Docker Engine

```json
{
  "experimental": false,
  "features": {
    "buildkit": true
  },
  "registry-mirrors": [
    "https://vmpi2mrp.mirror.aliyuncs.com"
  ]
}
```



# docker命令🐳

## 镜像操作

### 搜索镜像

```
docker search [image]
```



- OFFICIAL：是否为官方发布的镜像
- AUTOMATED：是否为自动构建

![image-20201222092118073](http://store.secretcamp.cn/uPic/image-2020122209211807320201222092118160860007879B7i879B7i8.png)





### 下载镜像

如果不加 tag 则默认下载 latest

```dockerfile
docker pull [image:tag]
```



### 查看镜像

```
docker images
```





### 删除镜像

```
docker rmi
```





## 容器操作

### 运行容器

```dockerfile
docker run [OPTIONS] image [COMMAND] [ARG...]

docker run --name mynginx -d nginx:latest  # 使用镜像nginx:latest以后台模式启动一个容器，命名为mynginx



参数列表：
  -d 后台运行
  -p [localhostPort:containerPort] 端口映射 
  -P 随机端口映射，容器内部端口随机映射到主机的端口
  -v [本地目录:容器目录] 把一个宿主机上的目录挂载到镜像里
  --link 添加链接到另一个容器
  --restart 指定在容器退出时，是否重启容器。容器平台创建的所有容器退出时，都会重启容器，所以本参数不需要特别指定
  --privileged 使用该参数，container内的root拥有真正的root权限
```







```
docker start [CONTAINER ID]
```



### 停止容器

```
docker stop [CONTAINER ID]
```



### 查看正在运行的容器

```sh
docker ps [OPTIONS]


参数列表：
  -a 显示所有容器，包括未运行的
  -s 显示总的文件大小
```



### 删除容器

```sh
docker rm
```





### 进入容器

```sh
docker attach [OPTIONS] CONTAINER
```

