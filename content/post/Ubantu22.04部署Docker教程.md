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


## 配置Docker用户组

[【Docker】报错：Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock - 06 - 博客园](https://www.cnblogs.com/allay/p/17544758.html#:~:text=%E5%8E%9F%E5%9B%A0%E5%88%86%E6%9E%90%EF%BC%9A%E8%BF%99%E6%98%AF%E5%9B%A0%E4%B8%BA%E4%BD%A0%E5%BD%93%E5%89%8D%E7%9A%84%E7%94%A8%E6%88%B7%E6%B2%A1%E6%9C%89%E8%BF%99%E4%B8%AA%E6%9D%83%E9%99%90%E3%80%82%20%E9%BB%98%E8%AE%A4%E6%83%85%E5%86%B5%E4%B8%8B%EF%BC%8Cdocker%20%E5%91%BD%E4%BB%A4%E4%BC%9A%E4%BD%BF%E7%94%A8%20Unix%20socket%20%E4%B8%8E%20Docker%20%E5%BC%95%E6%93%8E%E9%80%9A%E8%AE%AF%E3%80%82,Unix%20socket%E3%80%82%20%E5%87%BA%E4%BA%8E%E5%AE%89%E5%85%A8%E8%80%83%E8%99%91%EF%BC%8C%E4%B8%80%E8%88%AC%20Linux%20%E7%B3%BB%E7%BB%9F%E4%B8%8A%E4%B8%8D%E4%BC%9A%E7%9B%B4%E6%8E%A5%E4%BD%BF%E7%94%A8%20root%20%E7%94%A8%E6%88%B7%E3%80%82%20%E5%8D%B3%E6%88%91%E4%BB%AC%E5%BD%93%E5%89%8D%E7%9A%84%E7%94%A8%E6%88%B7%E4%B8%8D%E6%98%AFroot%E7%94%A8%E6%88%B7%E3%80%82)

默认情况下，docker 命令会使用 Unix socket 与 Docker 引擎通讯。而只有 root 用户和 docker 组的用户才可以访问 Docker 引擎的 Unix socket。出于安全考虑，一般 Linux 系统上不会直接使用 root 用户。即我们当前的用户不是root用户。

docker守护进程启动的时候，会默认赋予名字为docker的用户组读写Unix socket的权限，因此只要创建docker用户组，并将当前用户加入到docker用户组中，那么当前用户就有权限访问Unix socket了，进而也就可以执行docker相关命令

```sh
#添加docker用户组（一般此步可省略，docker会自动创建，） 
sudo groupadd docker 

#将用户加入到docker用户组，$USER为用户名。 
sudo gpasswd -a $USER docker 

#更新用户组
newgrp docker
```

重启电脑


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
--name nuplan_docker \
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

注意，运行容器的终端需要开启http代理


容器内取消代理
export 


## 退出 进入 容器

---

### 1. 退出容器

* 如果你在容器内部使用 `bash` 或其他 shell，想要退出容器，可以使用以下命令：

  ```bash
  exit
  ```

  这会让你退出当前的容器 shell 会话，容器仍然在后台运行（如果是交互模式且没有显式停止容器）。

  或者，如果你只是想暂时返回主机的命令行，也可以按 `Ctrl + P`，然后 `Ctrl + Q`，这样容器会在后台继续运行，而你会返回到主机的命令行。

---

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
---

### 3. 再次进入容器

你可以通过容器 ID 或名称再次进入容器。使用以下命令：

```bash
docker exec -it <container_id_or_name> /bin/bash
```


如果你希望进入容器的其他 shell（比如 `sh`），可以使用：

```bash
docker exec -it <container_id_or_name> /bin/sh
```

---

### 4. 进入已停止的容器

如果容器已经停止，你可以先重新启动容器，然后再进入容器：

```bash
docker start <container_id_or_name>
```

然后再次使用 `docker exec` 进入容器：

```bash
docker exec -it <container_id_or_name> /bin/bash
```

---



docker清除构建缓存
```sh
docker builder prune
```

清理未使用的镜像、容器、卷和网络
docker system prune

这条命令会删除：

未运行的容器（停止的容器）
悬挂镜像（没有标签的镜像）
未使用的网络
未使用的卷（可以加 --volumes 参数来删除未使用的卷）



nvidia/cuda:12.4.1-cudnn-devel-ubuntu22.04

装miniconda

创建nuplan环境

安装基础的编译工具pandas需要




docker导致的文件夹权限问题
容器中创建的文件或文件夹的属主是 root 用户，导致主机用户（如 fyq）没有权限修改或删除。



### ✅ **方法一：让容器中的进程使用你的主机用户（推荐）**

运行容器时指定 UID 和 GID：

```bash
docker run -u $(id -u):$(id -g) ...
```

这样，容器内创建的文件就会属于你当前的主机用户（比如 `fyq`），避免了 root 文件的问题。

**示例**：

```bash
docker run -u $(id -u):$(id -g) -v /home/fyq/data:/data my_image
```

> 💡 这适用于构建和开发类容器场景。部分需要 root 权限的服务（如 systemd、部分数据库）不适用。

---

### 🛠️ **方法二：容器内创建文件夹后，手动更改属主（一次性解决）**

假设容器运行后你发现主机文件夹 `/home/fyq/nuplan/exp/...` 是 root 拥有的。

你可以在主机上运行以下命令将属主改回自己：

```bash
sudo chown -R fyq:fyq /home/fyq/nuplan/exp
```

如果是具体某个文件夹：

```bash
sudo chown -R fyq:fyq /home/fyq/nuplan/exp/sim_DE_PDM_vis/
```

---

