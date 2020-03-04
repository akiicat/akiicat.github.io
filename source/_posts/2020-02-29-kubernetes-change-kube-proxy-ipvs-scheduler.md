---
title: 修改 Kubernetes Kube Proxy IPVS scheduler mode
tags:
  - Kubernetes
  - Kubectl
  - Kube Proxy
  - Ipvs
categories:
  - Kubernetes
date: 2020-02-29 22:38:14
---

由於 kube-proxy 在 ipvs scheduler 的模式是 rr，這篇會教學如何在 kubernetes cluster 裡面改變 ipvs scheduler 的模式。

## 環境

目前有三台 node 所組成的 kubernetes cluster

```shell
# kubectl get nodes -o wide
NAME    STATUS   ROLES    AGE    VERSION   INTERNAL-IP     EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
node1   Ready    master   5d7h   v1.16.6   192.168.0.121   <none>        Ubuntu 16.04.5 LTS   4.15.0-88-generic   docker://18.9.7
node2   Ready    master   5d7h   v1.16.6   192.168.0.146   <none>        Ubuntu 16.04.5 LTS   4.15.0-88-generic   docker://18.9.7
node3   Ready    <none>   5d7h   v1.16.6   192.168.0.250   <none>        Ubuntu 16.04.5 LTS   4.15.0-88-generic   docker://18.9.7
```

Kubernetes 裡面有一個 hello world deployment，並使用 NodePort 暴露 8080 port 的位置。

```shell
# kubectl get all
NAME                         READY   STATUS    RESTARTS   AGE
pod/hello-868dc85486-8lvps   1/1     Running   0          3d2h
pod/hello-868dc85486-j2q7x   1/1     Running   0          3d2h
pod/hello-868dc85486-n2lvt   1/1     Running   0          3d2h
pod/hello-868dc85486-njcfn   1/1     Running   0          3d2h

NAME                 TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)          AGE
service/kubernetes   ClusterIP      10.233.0.1      <none>          443/TCP          5d8h
service/node-port    NodePort       10.233.2.107    <none>          8080:31191/TCP   35h

NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/hello   4/4     4            4           3d2h

NAME                               DESIRED   CURRENT   READY   AGE
replicaset.apps/hello-868dc85486   4         4         4       3d2h
```

使用 ipvsadm 查看 ipvs 的 scheduler 模式，目前的模式是 rr。

```shell
# ipvsadm -ln
IP Virtual Server version 1.2.1 (size=4096)
Prot LocalAddress:Port Scheduler Flags
  -> RemoteAddress:Port           Forward Weight ActiveConn InActConn
TCP  169.254.25.10:31191 rr
  -> 10.233.90.16:8080            Masq    1      0          0         
  -> 10.233.92.16:8080            Masq    1      0          0         
  -> 10.233.92.17:8080            Masq    1      0          0         
  -> 10.233.96.12:8080            Masq    1      0          0         
TCP  172.17.0.1:31191 rr
  -> 10.233.90.16:8080            Masq    1      0          0         
  -> 10.233.92.16:8080            Masq    1      0          0         
  -> 10.233.92.17:8080            Masq    1      0          0         
  -> 10.233.96.12:8080            Masq    1      0          0         
TCP  192.168.0.121:31191 rr
  -> 10.233.90.16:8080            Masq    1      0          0         
  -> 10.233.92.16:8080            Masq    1      0          0         
  -> 10.233.92.17:8080            Masq    1      0          0         
  -> 10.233.96.12:8080            Masq    1      0          0
TCP  10.233.90.0:31191 rr
  -> 10.233.90.16:8080            Masq    1      0          0         
  -> 10.233.92.16:8080            Masq    1      0          0         
  -> 10.233.92.17:8080            Masq    1      0          0         
  -> 10.233.96.12:8080            Masq    1      0          0 
...
```

下一節將說明如何修改 ipvs scheduler 的方法。

## 方法

首先，IPVS scheduler 的設定是在 kube-proxy 的 ConfigMap 裡面。

```shell
kubectl edit cm kube-proxy -n kube-system
```

將 **data.config.conf.ipvs.scheduler** 裡的參數修改成其他的 scheduler 模式：

```yaml
data:
  config.conf:
    ipvs:
      scheduler: sh
```

在 [Service: IPVS proxy mode][1] 裡面有提供以下的 scheduler 的模式：

- rr: round-robin
- lc: least connection (smallest number of open connections)
- dh: destination hashing
- sh: source hashing
- sed: shortest expected delay
- nq: never queue

或參考 [IPVS wiki][2]

刪除原有的 kube-proxy 的 pod，daemonset.apps/kube-proxy 會自動重新建立新的 kube-proxy Pod。

```shell
kubectl delete -n kube-system $(kubectl get all -n kube-system | grep pod/kube-proxy | cut -d ' ' -f 1)
```

再次使用 ipvsadm 查看 ipvs 的 scheduler 就會是修改後的狀態。

```shell
# ipvsadm -ln
IP Virtual Server version 1.2.1 (size=4096)
Prot LocalAddress:Port Scheduler Flags
  -> RemoteAddress:Port           Forward Weight ActiveConn InActConn
TCP  169.254.25.10:31191 sh
  -> 10.233.90.16:8080            Masq    1      0          0         
  -> 10.233.92.16:8080            Masq    1      0          0         
  -> 10.233.92.17:8080            Masq    1      0          0         
  -> 10.233.96.12:8080            Masq    1      0          0         
TCP  172.17.0.1:31191 sh
  -> 10.233.90.16:8080            Masq    1      0          0         
  -> 10.233.92.16:8080            Masq    1      0          0         
  -> 10.233.92.17:8080            Masq    1      0          0         
  -> 10.233.96.12:8080            Masq    1      0          0         
TCP  192.168.0.121:31191 sh
  -> 10.233.90.16:8080            Masq    1      0          0         
  -> 10.233.92.16:8080            Masq    1      0          0         
  -> 10.233.92.17:8080            Masq    1      0          0         
  -> 10.233.96.12:8080            Masq    1      0          0
TCP  10.233.90.0:31191 sh
  -> 10.233.90.16:8080            Masq    1      0          0         
  -> 10.233.92.16:8080            Masq    1      0          0         
  -> 10.233.92.17:8080            Masq    1      0          0         
  -> 10.233.96.12:8080            Masq    1      0          0 
```

## Reference

- [Service: IPVS proxy mode][1]
- [IPVS: Connection Scheduling Algorithms inside the Kernel][1]

[1]: https://kubernetes.io/docs/concepts/services-networking/service/#proxy-mode-ipvs
[2]: http://kb.linuxvirtualserver.org/wiki/IPVS

