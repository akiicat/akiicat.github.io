---
title: Bare Metal 在 Ubuntu 上安裝 Kubernetes
tags:
  - Kubernetes
  - Docker
  - Installation
  - Ubuntu
  - Bare Metal
categories:
  - Kubernetes
date: 2019-04-18 11:55:59
---

## Docker

在安裝 Kubernetes 前要先安裝好 Docker，可以參考這篇：

{% post_link docker-ce-installation %}

## Kubeadm

kubeadm 負責管理節點，可以透過方便的指令將電腦加入 cluster，在這裡我們先定義：

- Master：代表主結點，負責控制與分發任務
- Node：代表子結點，負責執行 Master 所分發的任務

**[Master, Node]** 安裝 Kubeadm 需要 root 權限：

```shell
sudo su -
```

**[Master, Node]** 安裝 kubeadm：


```shell
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
echo "deb http://apt.kubernetes.io/ kubernetes-xenial main" > /etc/apt/sources.list.d/kubernetes.list
apt update
apt install -y kubeadm
```

apt 安裝 kubeadm 完後會連同 kubelet 跟 kubectl 一起安裝。

**[Master]** 在 **Master** 節點上初始化 Kubernetes：

```shell
kubeadm init --pod-network-cidr 10.244.0.0/16
```

因為我們是使用 flannel，所以必須加上 `--pod-network-cidr`。

>我們這邊選擇 flannel 的是因為 flannel 支援 arm。

如果要透過 WIFI 連接網路的話，需要加上 `--apiserver-advertise-address=<wifi-ip-address>` 參數到 `kubeadm init` 指令上。

執行 `kubeadm init` 之後會有一行 `kubeadm join`，如果弄丟的話，可以執行下面指令獲得：

```shell
kubeadm token create --print-join-command
```

**[Node]** 然後把其他的 node 加進來：

```shell
kubeadm join 192.168.0.11:6443 --token 3c564d.6q2we53btzqmf1ew \
    --discovery-token-ca-cert-hash sha256:a5480dcd68ec2ff27885932ac80d33aaa0390d295d4834032cc1eb554de3d5d2 
```

**[Master, Node]** 離開 **root**：

```shell
exit
```

## Kubectl

**[Master]** 回到使用者模式後執行：

```shell
mkdir -p $HOME/.kube
sudo cp -f /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

將 `admin.conf` 放置到 `~/.kube/config` 就會自動抓取設定檔。

**[Master]** 安裝 flannel，相關[文件](https://coreos.com/flannel/docs/latest/kubernetes.html)在 CoreOS 上：

```shell
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/bc79dd1505b0c8681ece4de4c0d86c5cd2643275/Documentation/kube-flannel.yml
```

**[Master]** 查看目前節點：

```shell
$ kubectl get nodes
NAME            STATUS   ROLES    AGE   VERSION
akiicat         Ready    master   77m   v1.14.1
akiicat-node2   Ready    <none>   51s   v1.14.1
```

## 測試

執行一些簡單的容器：

```shell
kubectl run kuard --image=gcr.io/kuar-demo/kuard-amd64:blue --replicas=3
```

查看 pods 是否有在運行

```shell
$ kubectl get pods
NAME                         READY   STATUS             RESTARTS   AGE
kuard-6cdb64fdcd-7bfgq       1/1     Running            0          17s
kuard-6cdb64fdcd-82mtv       1/1     Running            0          17s
kuard-6cdb64fdcd-rhxp2       1/1     Running            0          17s
```

使用 LoadBalancer 暴露它：

```shell
kubectl expose deployment kuard --type=LoadBalancer --port=80 --target-port=8080
```

查看 Service

```shell
$ kubectl describe service/kuard
Name:                     kuard
...
Type:                     LoadBalancer
IP:                       10.109.141.84
Port:                     <unset>  8080/TCP
TargetPort:               8080/TCP
NodePort:                 <unset>  30034/TCP
Endpoints:                10.244.1.5:8080,10.244.1.6:8080,10.244.1.7:8080
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
```

使用 curl 連到 pod 的 endpoint：

```shell
$ curl 10.244.1.5:8080
Hostname: kuard-1-6cdb64fdcd-7bfgq
...
```

## Trouble Shooting

### Swap Error

當你用 root 權限執行 `kubeadm init` 時，會出現 **ERROR Swap** 的錯誤：

```shell
# kubeadm init --pod-network-cidr 10.244.0.0/16
[init] Using Kubernetes version: v1.14.1
[preflight] Running pre-flight checks
[preflight] WARNING: Couldn't create the interface used for talking to the container runtime: docker is required for container runtime: exec: "docker": executable file not found in $PATH
error execution phase preflight: [preflight] Some fatal errors occurred:
	[ERROR FileContent--proc-sys-net-bridge-bridge-nf-call-iptables]: /proc/sys/net/bridge/bridge-nf-call-iptables does not exist
	[ERROR FileContent--proc-sys-net-ipv4-ip_forward]: /proc/sys/net/ipv4/ip_forward contents are not set to 1
	[ERROR Swap]: running with swap on is not supported. Please disable swap
[preflight] If you know what you are doing, you can make a check non-fatal with `--ignore-preflight-errors=...`
```

如果啟用 swap 的話，當你記憶體不夠用的時候，OS 會先把暫時沒有用的資料存到硬碟裡，又稱作為 swap out。相反，OS 需要用到剛剛存在硬碟裡的資料，則會把資料再載入回記憶體裡面，又稱作為 swap in。

使用 Kubenetes 的時候需要停用 swap 這個功能：

```shell
swapoff -a
```

相關討論在 Github 的 [issue](https://github.com/kubernetes/kubeadm/issues/610) 上。

上面的設定在重新開機之後就會失效 swap，要將 swap 完全關掉的話，需編輯 **/etc/fstab**  這個檔案，將 mount point 在 / 的項目註解掉：

```shell
# /etc/fstab
# UUID=10e56f7b-7b40-4b10-8029-642badc59ce9  /    ext4    errors=remount-ro 0       1
```

### exec format error

由於 CPU 有分 Intel 跟 Arm 的架構，這個問題會發生是因為 Docker image files 是基於某個特定的架構。也就是說，在 Intel 上建立的 Docker file 只能在 Intel 上執行；在 Arm32 上建立的 Docker file 只能在 Arm32 上執行。

所以當你使用 `kubectl logs` 查看某個 pod 出現如下的錯誤時：

```shell
$ kubectl logs pod/kuard-777c5775cd-lg7kc
standard_init_linux.go:207: exec user process caused "exec format error"
```

確認你的 Docker image 有支援你 CPU 的架構

Stackoverflow 上的[討論](https://stackoverflow.com/a/52793714/4777620)

## Reference

- [Kubernetes Officall Documentation: Setup Docker](https://kubernetes.io/docs/setup/cri/#docker)
- [Kubernetes Officall Documentation: Installing kubeadm kubelet kubectl](https://kubernetes.io/docs/setup/independent/install-kubeadm/#installing-kubeadm-kubelet-and-kubectl)
- [CoreOS Flannel](https://coreos.com/flannel/docs/latest/kubernetes.html)
- [Error Swap: running with swap on is not supported. Please disable swap](https://github.com/kubernetes/kubeadm/issues/610)
- [Error exec user process caused exec format error](https://stackoverflow.com/a/52793714/4777620)
- [How to safely turn off swap permanently and reclaim the space?](https://unix.stackexchange.com/a/224348)
