---
title: metallb-installation-for-minikube
date: 2019-04-17 12:15:50
tags:
  - Kubernetes
  - Minikube
  - Metallb
  - Load Balance
categories:
  - Kubernetes
---


## Metallb

MetalLB 是 Bare metal Kubernetes 的 load balancer，如果原先你使用 `--type=LoadBalancer` 來暴露你的 port 的話，service 裡的 `EXTERNAL-IP` 會一直是 pending 的狀態：

```shell
$ kubectl expose deployment hello-world --type=LoadBalancer
$ kubectl get service
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP    PORT(S)          AGE
hello-world  LoadBalancer   10.102.80.194   <pending>      3000:31118/TCP   17m
```

要解決這個方法其實有很多種，你可以使用現有的雲端平台，像是 GCP、AWS 等等，這些雲端平台上都有提供 Load Balancer 的服務，當然你需要付些費用，如果要免費的話，可以自己安裝 OpenStack。

但是我們只需要 Kubernetes 的功能，卻要把整個 OpenStack 安裝好，然後只用其中的 Load Balancer 的話，這個作法實在有點殺雞焉用牛刀。

如同 GitHub 上這篇 [issue](https://github.com/kubernetes/kubernetes/issues/36220) 所說的，為何不單把 Load Balancer 的服務抽出來安裝就好呢，於是 [metallb](https://github.com/google/metallb) 就這樣誕生了

## Installation

首先你必須先安裝好 minikube，minikube 的安裝就自行 google 了，而且還需要對 Kubernetes 的指令有一些基礎。

### BGP Router UI

查看 BGP router 狀態，你可以使用網頁查看 BGP router 目前的狀態

```shell
kubectl apply -f https://raw.githubusercontent.com/google/metallb/v0.7.3/manifests/test-bgp-router.yaml
```

使用 minikube 開啟網頁：

```shell
minikube service -n metallb-system test-bgp-router-ui
```

### Metallb

安裝最重要的 Metallb，他會在 `metallb-system` namespace 底下產生 daemonset `speaker` 跟 deployment `controller`。

```shell
kubectl apply -f https://raw.githubusercontent.com/google/metallb/v0.7.3/manifests/metallb.yaml
```

這時候重新刷新 BGP Router UI 的話，裡面的內容會跟安裝前的不一樣。

### ConfigMap

Metallb 的設定檔是透過 Kubernetes 裡面的 config map 下去做設定的，你必須跟他講他能夠分配的 IP 池，跟一些相關設定。

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    peers:
    - my-asn: 64500
      peer-asn: 64500
      peer-address: 10.96.0.100
    - my-asn: 64500
      peer-asn: 64500
      peer-address: 10.96.0.101
    address-pools:
    - name: my-ip-space
      protocol: bgp
      avoid-buggy-ips: true
      addresses:
      - 198.51.100.0/24
```

看到後面倒數幾行

- **avoid-buggy-ips: true**：代表 Load Balancer 在分配 IP 的時候，會從 `.1` 開始分配，而不會從 `.0` 開始
- **198.51.100.0/24**：Load Balancer 能分配的 IP 池

然後我們就套用這些設定吧：

```shell
kubectl apply -f https://raw.githubusercontent.com/google/metallb/v0.7.3/manifests/tutorial-3.yaml
```

## 檢查

來跑一些簡單的容器，然後使用 LoadBalancer 暴露它：

```shell
kubectl run hello-world --image=k8s.gcr.io/echoserver:1.10 --port=8080
kubectl expose deployment hello-world --type=LoadBalancer
```

使用 `kubectl get service` 指令查看現在 service 的狀態，可能需要等個幾秒鐘，`EXTERNAL-IP` 這個欄位就會是 metallb 從 IP 池分配給我們的 IP：

```shell
$ kubectl get service
NAME             TYPE           CLUSTER-IP      EXTERNAL-IP    PORT(S)          AGE
hello-world      LoadBalancer   10.103.246.36   198.51.100.1   8080:31504/TCP   11s
```

沒有看到 pending 的話，就代表你成功了。

## Reference

- [BGP on Minikube](https://metallb.universe.tf/tutorial/minikube/)