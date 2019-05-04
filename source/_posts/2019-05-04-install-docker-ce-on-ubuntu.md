---
title: 在 Ubuntu 18.04 上安裝 Docker CE
tags:
  - Docker
  - Ubuntu
  - Installation
categories:
  - Docker
date: 2019-05-04 14:54:53
---


安裝環境是在 Ubuntu 18.04 上。

## 安裝 Docker CE

docker.io 是 docker 的舊版本，如果先前有安裝要移除舊版本：

```shell
sudo apt-get remove docker docker-engine docker.io containerd runc
```

安裝相關套件：

```shell
sudo apt-get install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
```

匯入 docker apt repository：

```shell
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
   
sudo apt-get update
```

安裝 docker ce：

```shell
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```

建立 daemon：

```shell
# root
sudo cat > /etc/docker/daemon.json <<EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF

mkdir -p /etc/systemd/system/docker.service.d
```

啟用 Docker：

```shell
sudo systemctl start docker.service
sudo systemctl enable docker.service
```

加入使用者權限，加完後記得要重新開啟終端機：

```shell
sudo groupadd docker
sudo usermod -aG docker $USER
```

安裝完成

```shell
docker --version
```

## Reference

- [Get Docker CE for Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
- [Post-installation steps for Linux](https://docs.docker.com/install/linux/linux-postinstall/)
- [Kubernetes Officall Documentation: Setup Docker](