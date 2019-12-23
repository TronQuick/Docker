# Docker简介

Docker 是Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的容器中,然后发布到任何流行的Linux机器或Windows 机器上,也可以实现虚拟化,容器是完全使用沙箱机制,相互之间不会有任何接口。

- 可粗糙地理解为虚拟机，但不是虚拟机



# 常用指令

## docker服务启停篇

- **1.启动docker**：

```
sudo systemctl start docker
```

- **2.守护进程重启**：

```
sudo systemctl daemon-reload
```

- **3.重启docker**：

```
sudo systemctl restart  docker
```

- **4.关闭docker**：

```
sudo systemctl stop docker  
```

- **5.重启docker服务**：

```
sudo service docker restart
```

- **6.关闭docker服务**：

```
sudo service docker stop   
```

## docker常用命令篇

- **1.搜索镜像**

```
docker search 镜像名称
```

- **2.拉取/下载镜像**

```
docker pull 镜像名:TAG 
```

- **3.查看已安装镜像列表**

```
docker images
```

- **4.删除镜像**

```
docker rmi 镜像id

docker rmi 镜像名:TAG
```

- **5.运行镜像生成新的容器**

```
docker run --name=test -itd 镜像名:TAG
```

- 例子1:

```
docker run --name=test -itd -p 8080:8080 tomcat:7
```

- 例子1说明：
  - -p：端口映射。**前者为宿主机端口，后者为容器端口**。多个端口映射示例：`-p 8080:8080 -p 9090:9090`。
  - -d：后台模式运行容器。
  - -it：以交互模式运行容器。
  - --name=test：给容器命名。

```
提示：-itd 通常连着用
```

- 例子2:

```
docker run --name=test -itd -p 8080:8080 -v /opt:/opt tomcat:7
```

- 例子2说明：
  - -v：数据挂载。前者为宿主机路径，冒号后面为容器路径。

------

- **6.进入容器**

```
docker exec -it  容器id或者容器名  /bin/bash
这里 /bin/bash为默认，也可以为bash或者sh。
```

- **7.容器的启动停止**

```
docker start 容器id或者容器名
docker stop 容器id或者容器名 
```

- **8.删除容器**

```
docker rm 容器id或者容器名 
```

- **9.查看容器日志**

```
docker logs 容器id或者容器名 -f
```

------

- **10.Dockerfile方式构建镜像**

```
docker  build -t  镜像名:TAG .
```

- 例子：

```
docker  build -t minijava8:u231 .
```

- **11.容器提交方式构建镜像**

```
docker commit -a="作者" -m="提交的描述信息" 容器id或者容器名字 镜像名:TAG
```

- 例子

```
docker commit -a="Lain" -m="测试通过提交容器创建镜像" minijava mynewimage:v1
```

- **12.推送镜像至阿里云**

```
sudo docker login --username=用户名 registry.cn-shenzhen.aliyuncs.com
sudo docker tag [ImageId] registry.cn-shenzhen.aliyuncs.com/[命名空间]/[镜像名]:[镜像版本号]
sudo docker push registry.cn-shenzhen.aliyuncs.com/[命名空间]/[镜像名]:[镜像版本号]
提示：该命令可以去阿里云容器镜像服务查看，更加准确。
```

------

## 黑科技篇

- **1.查看Docker容器网络Ip**：

```
docker inspect -f='{{.NetworkSettings.IPAddress}}'  [容器id或容器名]
```

- **2.批量删除Docker容器镜像**：

1. 停止所有容器：

```
docker stop $(docker ps -a -q)
```

1. 删除所有容器：

```
docker rm $(docker ps -a -q)
```

1. 想要删除untagged images，也就是那些id为`<None>`的image的话可以用

```
docker rmi  $(docker images | grep "^<none>" | awk -F" " '{print $3}')
```

1. 删除全部image

```
docker rmi $(docker images -q)
```

- **3.配置镜像加速器**

针对Docker客户端版本大于`1.10.0`的用户，您可以通过修改**daemon配置文件**`/etc/docker/daemon.json`来使用加速器

```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://vuog38en.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```