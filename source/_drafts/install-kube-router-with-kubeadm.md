---
title: 用 kubeadm 安裝 kube-router
categories:
  - Kubernetes
tags:
  - Kubernetes
  - Kubeadm
  - Kube Router
---

## 安裝 Kubernetes

```shell
kubeadm init --pod-network-cidr 10.244.0.0/16
```

## 安裝 Kube Router



```shell
kubectl apply -f https://raw.githubusercontent.com/cloudnativelabs/kube-router/master/daemonset/kubeadm-kuberouter.yaml
```





## Reference

- [Deploying kube-router with kubeadm](https://github.com/cloudnativelabs/kube-router/blob/master/docs/kubeadm.md)
- [Installing a pod network add-on](https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/#pod-network)

