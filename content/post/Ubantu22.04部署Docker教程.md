---
title: "Ubantu22.04部署Docker教程"
date: "2025-06-05T00:00:00Z"
math: true
categories:
    - 工作
tags:
    - 技术
---

## 安装docker engine，不要安装docker desktop

- Docker Desktop 是针对 Windows 和 Mac 用户设计的应用，它包含了 Docker 引擎（Docker Engine）和 Docker CLI 工具。对于这些操作系统，Docker Desktop 会启用一个虚拟机（通过 Hyper-V 或 WSL 2）来运行容器，而不是像 Linux 系统那样直接在宿主机上运行 Docker 服务。
- 在 Linux 系统 上，你需要使用 Docker Engine，而不是 Docker Desktop。
- 此外网上大多数教程都是针对Docker Engine的。

## Ubantu安装教程

[Ubuntu \| Docker Docs](https://docs.docker.com/engine/install/ubuntu/)

直接运行
```sh
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<EOF
{
    "registry-mirrors": [
        "https://docker.xuanyuan.me"
    ]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## Docker加速

### 镜像

[2025 Docker/DockerHub 国内镜像源/加速列表](https://zhuanlan.zhihu.com/p/24461370776)
[Docker 国内镜像加速地址-长期更新(2025-06) - 讲文张字 - 博客园](https://www.cnblogs.com/zhangwencheng/p/18909645)

这些镜像通常只能pull不能search，要search直接去dockerhub网站，要在终端可以用下面的方法：配置代理


### 配置代理

#### 方法一

```sh
sudo mkdir -p /etc/systemd/system/docker.service.d
sudo nano /etc/systemd/system/docker.service.d/http-proxy.conf
```

添加以下内容并保存，端口号`7890`根据电脑实际情况修改
```
[Service]
Environment="HTTP_PROXY=http://127.0.0.1:7890/"
Environment="HTTPS_PROXY=http://127.0.0.1:7890/"
Environment="NO_PROXY=192.168.*,172.31.*,172.30.*,172.29.*,172.28.*,172.27.*,172.26.*,172.25.*,172.24.*,172.23.*,172.22.*,172.21.*,172.20.*,172.19.*,172.18.*,172.17.*,172.16.*,10.*,127.*,localhost"
```

重新加载 systemd 并重启 Docker
```sh
sudo systemctl daemon-reload
sudo systemctl restart docker
```

#### 方法二

配置全局代理
```bash
sudo vim ~/.docker/config.json
```

配置 HTTP 和 HTTPS 代理：
```
{
 "proxies": {
   "default": {
     "httpProxy": "http://proxy.example.com:3128",
     "httpsProxy": "https://proxy.example.com:3129",
     "noProxy": "*.test.example.com,.example.org,127.0.0.0/8"
   }
 }
}
```

开启容器代理
```sh
--env HTTP_PROXY="http://172.18.0.1:7897/"\
--env HTTPS_PROXY="http://172.18.0.1:7897/"\
--env http_proxy="http://172.18.0.1:7897/"\
--env https_proxy="http://172.18.0.1:7897/"\
--env NO_PROXY="localhost,127.0.0.1,.example.com"\
```

查看docker网卡地址，运行`ifconfig`
假设得到docker0地址为：172.18.0.1

`http://proxy.example.com` 填172.18.0.1

## 安装 NVIDIA Container Toolkit

[Installing the NVIDIA Container Toolkit — NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html)

安装完成之后配置docker
```sh
sudo nvidia-ctk runtime configure --runtime=docker
```

重启docker服务
```sh
sudo systemctl restart docker
```




## 下载镜像

去dockerhub搜索
[Docker Hub Container Image Library \| App Containerization](https://hub.docker.com/)

找到想要的镜像之后进行docker pull，可以直接copy code运行，举个例子
```sh
docker pull nginx:stable-perl
```

## 运行镜像
```sh
docker run --gpus all -itd \
-p 30000:30000 \
-p 5006:5006 \
-v /home/fyq/SAH-Drive:/root/SAH-Drive \
-v /home/fyq/nuplan:/root/nuplan \
-v /home/fyq/interplan_workspace:/root/interplan_workspace \
--name nuplan_docker1 \
--env http_proxy="http://172.18.0.1:7897/" \
--env https_proxy="http://172.18.0.1:7897/" \
--env NO_PROXY="localhost,127.0.0.1,.example.com" \
sah_nuplan_image:latest /bin/bash 
```

- --gpus all：将所有 GPU 暴露给容器。也可以指定 GPU 数量或 ID，比如 --gpus '"device=0,1"'
- -it：交互模式，进入容器
- -d 让容器在后台运行，保持容器常开
- nvidia/cuda:12.4.1-runtime-ubuntu22.04：你的镜像名称，比如 my-gpu-image
- /bin/bash：进入容器 shell（可按需修改为运行的脚本或命令）
- --name: 给容器起的名字
- --env 环境变量



注意，运行容器的终端需要开启http代理




## 退出 进入 容器


### 1. 退出容器

* 如果你在容器内部使用 `bash` 或其他 shell，想要退出容器，可以使用以下命令：

  ```bash
  exit
  ```

  这会让你退出当前的容器 shell 会话，容器仍然在后台运行（如果是交互模式且没有显式停止容器）。

  或者，如果你只是想暂时返回主机的命令行，也可以按 `Ctrl + P`，然后 `Ctrl + Q`，这样容器会在后台继续运行，而你会返回到主机的命令行。


### 2. 查看正在运行的容器

如果你退出了容器，但想再次进入容器，首先需要确认容器是否在运行。可以使用以下命令查看当前正在运行的容器：

```bash
docker ps
```

这将显示所有正在运行的容器，包括容器 ID 和名称等信息。

```bash 
docker ps -a
```

这将显示所有的容器，包括没有运行的。

### 3. 再次进入容器

你可以通过容器 ID 或名称再次进入容器。使用以下命令：

```bash
docker exec -it <container_id_or_name> /bin/bash
```


### 4. 进入已停止的容器

如果容器已经停止，你可以先重新启动容器，然后再进入容器：

```bash
docker start <container_id_or_name>
```

然后再次使用 `docker exec` 进入容器：

```bash
docker exec -it <container_id_or_name> /bin/bash
```


## 缓存清理

docker清除构建缓存
```sh
docker builder prune
```

清理未使用的镜像、容器、卷和网络
docker system prune

这条命令会删除：
- 未运行的容器（停止的容器）
- 悬挂镜像（没有标签的镜像）
- 未使用的网络
- 未使用的卷（可以加 --volumes 参数来删除未使用的卷）



docker导致的文件夹权限问题
容器中创建的文件或文件夹的属主是 root 用户，可能导致主机用户（如 fyq）没有权限修改或删除。

假设容器运行后你发现主机文件夹 `/home/fyq/nuplan/exp/...` 是 root 拥有的。

你可以在主机上运行以下命令将属主改回自己：

```bash
sudo chown -R fyq:fyq /home/fyq/nuplan/exp
```

## 拷贝文件进入容器
```bash
docker cp path/host path/container
```

`docker cp` 有一个已知限制：它不能递归地覆盖已有的目录结构中的单个文件，尤其是容器内目标路径已经存在且非空的情况下。
只能先删除容器内的目标目录，再copy。


## dockerfile常用命令


### 1. `FROM`：指定基础镜像（必须的第一行）

```dockerfile
FROM ubuntu:20.04
```

### 2. `RUN`：在镜像中执行命令（用于安装软件等）

```dockerfile
RUN apt-get update && apt-get install -y python3
```

### 3. `COPY`：将本地文件/目录复制到镜像中

```dockerfile
COPY ./app /usr/src/app
```

### 4. `ADD`：类似 `COPY`，但支持自动解压 `.tar` 文件及远程下载

```dockerfile
ADD https://example.com/file.tar.gz /tmp/
```

### 5. `WORKDIR`：设置工作目录（后续命令会在此目录下执行）

```dockerfile
WORKDIR /usr/src/app
```

### 6. `CMD`：指定容器启动时默认执行的命令（只生效一次）

```dockerfile
CMD ["python3", "main.py"]
```

### 7. `ENTRYPOINT`：指定容器启动命令（常用于将容器当作命令运行）

```dockerfile
ENTRYPOINT ["python3", "main.py"]
```

### 8. `ENV`：设置环境变量

```dockerfile
ENV PATH="/opt/conda/bin:$PATH"
ENV LANG C.UTF-8
```

### 9. `EXPOSE`：声明容器监听的端口（不自动映射）

```dockerfile
EXPOSE 8080
```

### 10. `VOLUME`：声明挂载点（用于持久化数据）

```dockerfile
VOLUME ["/data"]
```

### 11. `ARG`：构建时传参（与 `ENV` 区别是生命周期）

```dockerfile
ARG VERSION=1.0
RUN echo "Version is $VERSION"
```

从dockerfile构建容器
```bash
docker build -t your-image-name .
```