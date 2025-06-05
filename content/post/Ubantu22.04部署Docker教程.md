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

找到想要的镜像之后进行docker pull，可以直接copy code运行

