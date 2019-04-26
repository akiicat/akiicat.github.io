---
title: 在 Ubuntu 上重新安裝 Kubernetes
tags:
  - Kubernetes
  - Ubuntu
  - Bare Metal
  - Installation
categories:
  - Kubernetes
date: 2019-04-26 18:29:00
---


## 介紹

Kubeadm 有提供一個指令 **reset**，不過他只會將有關 Kubernetes 的東西刪除，像是 **flannel**、**cni** 的網路設定，則必須要手動刪除。

這裡使用的環境是：

- Ubuntu 18.04
- Kubernetes 1.14.1
- Flannel 0.10.0

## Problem

要讓問題重現，只需要在你安裝好 Kubernetes Cluster 之後，重設 Kubernetes 就會發生：

```shell
kubeadm reset -f
kubeadm init
```

這個時候你的 coredns 會一直在 pending 的狀態，而且 nodes 會一直是 NodReady：

```shell
$ kubectl get nodes
NAME      STATUS     ROLES    AGE   VERSION
akiicat   NotReady   master   65m   v1.14.1

$ kubectl get pod -n kube-system
NAME                      READY   STATUS    RESTARTS   AGE
coredns-fb8b8dccf-2t48c   0/1     Pending   0          42s
coredns-fb8b8dccf-x7f87   0/1     Pending   0          42s
```

看一下 **kubelet** 是什麼問題，猜測是之前的 CNI 沒有清除乾淨，而套用到舊的資料

```shell
$ systemctl status kubelet
...
 4月 25 14:01:40 akiicat kubelet[6416]: W0425 14:01:40.779474    6416 cni.go:213] Unable to update cni config: No networks found in /etc/cni/net.d
 4月 25 14:01:40 akiicat kubelet[6416]: E0425 14:01:40.901231    6416 kubelet.go:2170] Container runtime network not ready: NetworkReady=false reason:NetworkPluginNotReady message:docker: network plugin is not ready: cni config uninitialized
```

## Step by Step

接下來就一步一步的解決這個問題，首先切換成 root 權限：

```shell
sudo su -
```

先把 Kubernetes 重設，**-f** 參數代表強制執行 reset，不會跳出提示訊息的確認：

```shell
kubeadm reset -f
```

停止 **kubelet**、**docker**：

```shell
systemctl stop kubelet
systemctl stop docker
```

完全刪除 **cni**、**flannel** 的資料：

```shell
rm -rf /var/lib/cni/
rm -rf /var/lib/kubelet/*
rm -rf /run/flannel
rm -rf /etc/cni/
```

移除 **cni**、**flannel** 的網路介面卡：

```shell
ifconfig cni0 down
brctl delbr cni0
ifconfig flannel.1 down
```

重新啟動 **docker**：

```shell
systemctl start docker
```

這樣就完成了，最後檢查一下網路介面卡與 IP table 有沒有 **flannel**、**cni**：

```shell
ifconfig
route -n
```

沒有在這上面就成功了。

後續安裝可以參考我寫的這篇文章：{% post_link kubernetes-installation-on-ubuntu %}

## Summary

最後要輸入指令的時候，需要對 Master 跟 Worker 執行不同的指令，以及在不同的權限下執行：

- Master：代表主結點。
- Node：代表 Worker 節點或子結點。

**[Master, Node]** 不管事 master 跟 worker 都要執行 Kubernetes Reset，在執行時要注意權限是否正確：

```shell
# root
kubeadm reset -f
systemctl stop kubelet
systemctl stop docker
rm -rf /var/lib/cni/
rm -rf /var/lib/kubelet/*
rm -rf /run/flannel
rm -rf /etc/cni/
ifconfig cni0 down
brctl delbr cni0
ifconfig flannel.1 down
systemctl start docker
```

**[Master]** 安裝 Kubernetes：

```shell
# root
kubeadm init --pod-network-cidr 10.244.0.0/16
```

注意要切換使用者：

```shell
# user
mkdir -p $HOME/.kube
sudo cp -f /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/bc79dd1505b0c8681ece4de4c0d86c5cd2643275/Documentation/kube-flannel.yml
```

**[Node]** 加入 worker 節點：

```shell
# root
kubeadm join 192.168.0.11:6443 --token 3c564d.6q2we53btzqmf1ew \
    --discovery-token-ca-cert-hash sha256:a5480dcd68ec2ff27885932ac80d33aaa0390d295d4834032cc1eb554de3d5d2 
```

## Reference

- [Kubernetes cannot cleanup Flannel](https://stackoverflow.com/questions/46276796/kubernetes-cannot-cleanup-flannel)
- [k8s / kubeadm: Joining cluster takes forever](https://stackoverflow.com/questions/55410863/k8s-kubeadm-joining-cluster-takes-forever)

