---
title: RBAC Kubernetes 安裝 helm
tags:
  - Kubernetes
  - Helm
  - Tiller
  - Installation
categories:
  - Kubernetes
date: 2019-07-04 01:32:54
---


## 環境

- Google Kubernetes Engine
- Kubernetes version 1.12.8
- Helm version 2.14.1

## 安裝 Helm without RBAC

Helm 是 Kubernetes 的管理工具，Tiller 則是 API server，至於之間的差別，Helm 是 Client 端，Tiller 是 Server 端。輸入以下指令會安裝 **Helm** 和 **Tiller**：

```shell
helm init
```

官方文件建議加上 **--history-max** 參數，限制最大歷史指令的數量，如果沒有設定最大歷史記錄，則永遠保留歷史記錄。

```shell
helm init --history-max 200
```

##設定 RBAC

在雲端平台上，由於有安全性的問題，如果不設定 RBAC，之後可能會無法正常使用，所以需要提供足夠的權限給 tiller，輸入以下指令建立權限，[ServiceAccount](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/) 的名稱為 **tiller**：

```shell
kubectl create serviceaccount --namespace kube-system tiller
kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
```

套用 tiller 的 ServiceAccount 到已經現有的上

```shell
kubectl patch deployment --namespace kube-system tiller-deploy -p '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}'
```

## 安裝 Helm with RBAC

所有設定一次到位，在 **helm init** 的時候，加了 **--service-account** 參數，直接套用 ServiceAccount：

```shell
kubectl create serviceaccount --namespace kube-system tiller
kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
helm init --service-account tiller --history-max 200
```

## 測試

```shell
$ helm install --name my-db stable/influxdb
NAME:   my-db
LAST DEPLOYED: Wed Jul  3 23:13:08 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME            DATA  AGE
my-db-influxdb  1     1s

==> v1/PersistentVolumeClaim
NAME            STATUS   VOLUME    CAPACITY  ACCESS MODES  STORAGECLASS  AGE
my-db-influxdb  Pending  standard  1s

==> v1/Pod(related)
NAME                             READY  STATUS   RESTARTS  AGE
my-db-influxdb-689d74646c-6fwc4  0/1    Pending  0         1s

==> v1/Service
NAME            TYPE       CLUSTER-IP     EXTERNAL-IP  PORT(S)            AGE
my-db-influxdb  ClusterIP  10.11.249.197  <none>       8086/TCP,8088/TCP  1s

==> v1beta1/Deployment
NAME            READY  UP-TO-DATE  AVAILABLE  AGE
my-db-influxdb  0/1    1           0          1s

...
```

查看套件清單

```shell
$ helm ls
NAME    REVISION        UPDATED                         STATUS          CHART           APP VERSION     NAMESPACE
my-db   1               Wed Jul  3 23:13:08 2019        DEPLOYED        influxdb-1.1.9  1.7.6           default
```

刪除測試檔案，**--purge** 參數可以徹底刪除，不留下任何記錄，名稱可以透過上面的指令查詢：

```shell
helm delete --purge my-db
```

## 解除安裝 Helm

```shell
 kubectl -n kube-system delete deployment tiller-deploy
 kubectl delete clusterrolebinding tiller
 kubectl -n kube-system delete serviceaccount tiller
```

## Reference

- [Helm Quickstart Guide](https://helm.sh/docs/using_helm/#quickstart-guide)
- [Easily install/uninstall Helm on RBAC Kubernetes](https://medium.com/@pczarkowski/easily-install-uninstall-helm-on-rbac-kubernetes-8c3c0e22d0d7)

