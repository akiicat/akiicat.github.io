---
title: Kubernetes MetalLB Load Balancer 設定多個 address pool
tags:
  - Kubernetes
  - MetalLB
  - Load Balance
categories:
  - Kubernetes
date: 2019-04-22 15:37:56
---


## Before Start

首先你先要安裝好：

- [Kubernetes 1.9](https://kubernetes.io/docs/setup/independent/install-kubeadm/#installing-kubeadm-kubelet-and-kubectl) 版以上，以及可以使用 **kubeadm**、**kubectl** 、**kubelet** 指令
- [MetalLB](https://metallb.universe.tf/tutorial/layer2/) Layer2 Load Balancer

由於我們會使用 **bgp** 的 protocol，可以直接在 minikube 上使用，如果你是在 bare metal 上操作的話，必須做額外的設定，可以參考[官方文件](https://metallb.universe.tf/configuration/#bgp-configuration)。

## Configure Load Balancer

MetalLB 設定 Load Balancer 需要提供 ConfigMap 裡面包涵我們給定的網段範圍

會需要分別使用不同的 address pool 是有原因的，有申請過網路的都知道，固定 IP 是很貴的，而且數量有限，只會在有需要的時候才會使用公開固定 IP 網域池，像是產品 production 的網頁。

至於其他還在測試 staging 的網頁，使用私人網域池即可，最後在設定個 VPN 連線進來看，就能夠減少申請固定 IP 的費用。

這裡我們建立了兩個網域池，相關的設定如下：

```yaml
# configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    address-pools:
    - name: production
      protocol: bgp
      auto-assign: false
      addresses:
      - 17.95.16.28-17.95.16.32
    - name: staging
      protocol: bgp
      avoid-buggy-ips: true
      addresses:
      - 192.168.144.0/20
```



|      | address pool 1          | address pool 2   |
| ---- | ----------------------- | ---------------- |
| 名稱 | production              | staging          |
| 協定 | bgp                     | bgp              |
| 網段 | 17.95.16.28-17.95.16.32 | 192.168.144.0/20 |

這裡假設我們申請到這個範圍的固定 IP 17.95.16.28-17.95.16.32，可以供 Load Balancer 來分配。

至於設定 `avoid-buggy-ips: true` 參數的話， MetalLB 避免 **.0** 跟 **.255** 的 IP。

如果想要手動指派 IP 的話，可以設定成 `auto-assign: false`，不設定的話預設是 `true`。

寫好設定檔，我們就立即套用它：

```shell
kubectl apply -f configmap.yaml
```

## 測試

現在我們來測試剛剛設定的 Load Balancer 有沒有成功。

我會執行兩份 deployment，一個會使用 **production** 的網域池，另一個會使用 **staging** 的網域池，兩個設定檔分別是：

```yaml
# hello-production.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-production
  labels:
    run: hello-production
spec:
  replicas: 3
  selector:
    matchLabels:
      run: hello-production
  template:
    metadata:
      labels:
        run: hello-production
    spec:
      containers:
      - name: hello-production
        image: k8s.gcr.io/echoserver:1.10
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: hello-production
  annotations:
    metallb.universe.tf/address-pool: production
spec:
  ports:
  - port: 8080
    targetPort: 8080
  selector:
    run: hello-production
  type: LoadBalancer
```

```yaml
# hello-staging.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-staging
  labels:
    run: hello-staging
spec:
  replicas: 3
  selector:
    matchLabels:
      run: hello-staging
  template:
    metadata:
      labels:
        run: hello-staging
    spec:
      containers:
      - name: hello-staging
        image: k8s.gcr.io/echoserver:1.10
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: hello-staging
  annotations:
    metallb.universe.tf/address-pool: staging
spec:
  ports:
  - port: 8080
    targetPort: 8080
  selector:
    run: hello-staging
  type: LoadBalancer
```

這裡需要在 **metadata.annotations** 裡面設定 **metallb.universe.tf/address-pool: staging** 參數，後面接值必須跟前面 ConfigMap 裡 address pool 的名稱一樣。

```yaml
metadata:
  annotations:
    metallb.universe.tf/address-pool: staging
```

然後套用設定：

```shell
kubectl apply -f hello-production.yaml,hello-staging.yaml
```

查看我們現在正在運行的容器與網路設定：

```shell
$ kubectl get pods
NAME                               READY   STATUS    RESTARTS   AGE
hello-production-b58c4488d-fxj7d   1/1     Running   0          19m
hello-production-b58c4488d-kx2nh   1/1     Running   0          19m
hello-production-b58c4488d-tp8f6   1/1     Running   0          19m
hello-staging-c8fc9f69d-4gmfr      1/1     Running   0          19m
hello-staging-c8fc9f69d-rhh7q      1/1     Running   0          19m
hello-staging-c8fc9f69d-vdwt2      1/1     Running   0          19m
$ kubectl get service
NAME               TYPE           CLUSTER-IP       EXTERNAL-IP      PORT(S)          AGE
hello-production   LoadBalancer   10.109.99.105    17.95.16.28      8080:31532/TCP   19m
hello-staging      LoadBalancer   10.110.199.100   192.168.144.1    8080:32603/TCP   19m
```

有被分配到外部固定 IP 的 service，就能夠使用透過外面的網路連進來，可以馬上找其他電腦來試試。

## Reference

- [MetalLB Usage Full example](https://metallb.universe.tf/usage/example/)