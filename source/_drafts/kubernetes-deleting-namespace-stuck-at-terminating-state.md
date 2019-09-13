---
title: 解決 Kubernetes 在刪除 Namespace 時會卡在 Terminating 的狀態
categories:
  - Kubernetes
tags:
  - Kubernetes
  - Trouble Shooting
---



事情發生的經過：

```shell
kubectl create namespace kuard
kubectl run kuard -n kuard --image=gcr.io/kuar-demo/kuard-amd64:blue --replicas=3
```



如果這時候輸入：

```shell
kubectl delete namespace kuard
```

會卡住在這個畫面：

```shell
$ kubectl delete namespace kuard
namespace "kuard" deleted
```







```shell
kubectl delete namespace kuard --force --grace-period 0
```





## Reference

- [Deleting namespace stuck at Terminating state](https://github.com/kubernetes/kubernetes/issues/60807#)

