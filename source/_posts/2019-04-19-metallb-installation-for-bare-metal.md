---
title: MetalLB 在 Kubernetes Bare Metal 上安裝 Layer 2 Load Balancer
date: 2019-04-19 10:58:46
categories:
  - Kubernetes
tags:
  - Kubernetes
  - Metallb
  - Load Balance
  - Bare Metal
---


## 準備

假設你已經先裝好 Kubernetes cluster 了，由於是 bare metal，你必須把 Kubernetes 裝載三台不同電腦上，或是使用三個  Raspberry Pi，然後使用 `kubeadm` 指令串起來。

## Cluster Addresses

我們現在有三台電腦，它的名稱與 IP 分別是：

- **Master**：**192.168.1.100**

- **Node1**：**192.168.1.101**

- **Node2**：**192.168.1.102**

  >  當你在安裝電腦時，名稱與 IP 必須是唯一的，否則在 join 的時候會出現錯誤。

這裡使用的 IP 是 DHCP 所分配的 IP，IP 的範圍是 `192.168.1.100—192.168.1.150`，如果你有足夠的 public IP address，可以分別對每台電腦做設定。

> 假設我申請了 `17.95.16.0-17.95.16.32`，就可以把 IP 設定成這樣：
>
> - **Master**：**17.95.16.1**
> - **Node1**：**17.95.16.2**
> - **Node2**：**17.95.16.3**
>
> 雖然只用了三個，但我們保留剩下的 `17.95.16.10-17.95.16.32` 讓 Load Balancer 來分配這些網址

## Metallb

安裝 metallb 的步驟非常的簡單：

```shell
kubectl apply -f https://raw.githubusercontent.com/google/metallb/v0.7.3/manifests/metallb.yaml
```

然後查看目前的狀態，是否有一個 controller 跟多個 speaker：

```shell
$ kubectl get pods -n metallb-system
NAME                         READY   STATUS    RESTARTS   AGE
controller-cd8657667-2znm8   1/1     Running   0          22h
speaker-m95vd                1/1     Running   0          22h
speaker-shq8s                1/1     Running   0          22h
```

因為我們還沒有提供 load balance 的網址給他，所以接下來要設定 ConfigMap

## Configure

架設我們想提供 load balance 的網址是 `192.168.1.240-192.168.1.250`，看一下我們的設定檔：

```shell
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    address-pools:
    - name: my-ip-space
      protocol: layer2
      addresses:
      - 192.168.1.240-192.168.1.250
```

我們把 `protocol: layer2` 套用在 `192.168.1.240-192.168.1.250` 這個網段上

>如果想要使用外網的話，直接把網段的範圍改成外網即可

MetalLB 提供了 **layer2** 跟 **bgp** 兩種 protocol，這裡使用 layer2，如果使用 bgp 的話還需要另外做設定，可以參考[官方文件](https://metallb.universe.tf/configuration/#bgp-configuration)。

套用上面的 ConfigMap：

```shell
kubectl apply -f https://raw.githubusercontent.com/google/metallb/v0.7.3/manifests/example-layer2-config.yaml
```

查看 Log：

```shell
kubectl logs -l component=speaker -n metallb-system
```

## 測試

執行一些簡單的容器，然後使用 LoadBalancer 暴露它：

```shell
kubectl run hello-world --image=k8s.gcr.io/echoserver:1.10 --port=8080 --replicas=3
kubectl expose deployment hello-world --type=LoadBalancer
```

查看 pods 是否有在運行

```shell
$ kubectl get pods
NAME                                 READY   STATUS             RESTARTS   AGE
pod/hello-world-6cdb64fdcd-7bfgq   1/1     Running            0          17s
pod/hello-world-6cdb64fdcd-82mtv   1/1     Running            0          17s
pod/hello-world-6cdb64fdcd-rhxp2   1/1     Running            0          17s
$ kubectl get service
NAME          TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)          AGE
hello-world   LoadBalancer   10.106.40.173   192.168.1.240    8080:30700/TCP   11s
```

我們現在有外部 IP 了！馬上測試看看能不能連得到：

```shell
$ curl 192.168.1.240:8080
Hostname: hello-world-1-6cdb64fdcd-7bfgq
...
```

如果有看到跟請求 http 有關的資訊的話，就代表你成功了。

## Reference

- [Metallb Layer 2 Mode Tutorial](https://metallb.universe.tf/tutorial/layer2)

