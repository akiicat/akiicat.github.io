---
title: 在本地端上建立 Kubernetes Dashboard
tags:
  - Kubernetes
  - Dashboard
categories:
  - Kubernetes
date: 2019-04-23 10:29:27
---


## Installation

在安裝 Dashboard 之前，請先確認你的 Kubernetes 已經裝好了，至於怎麼安裝 Kubernetes 就自行 Google 拉。

接下來我們要開始安裝 [Github Kubernetes Dashboard](https://github.com/kubernetes/dashboard)，官網上其實已經說明的很清楚，不過這裡在記錄一下安裝的過程：

```shell
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml
```

沒有錯，這樣就安裝好了，然後看一下正在運行的容器：

```shell
$ kubectl get all -n kube-system -l k8s-app=kubernetes-dashboard
NAME                                        READY   STATUS    RESTARTS   AGE
pod/kubernetes-dashboard-5f7b999d65-sswt4   1/1     Running   0          84m

NAME                           TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
service/kubernetes-dashboard   ClusterIP   10.102.216.229   <none>        443/TCP   84m

NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/kubernetes-dashboard   1/1     1            1           84m

NAME                                              DESIRED   CURRENT   READY   AGE
replicaset.apps/kubernetes-dashboard-5f7b999d65   1         1         1       84m
```

## Access

接下來我們要連到 Dashboard，先把 **kubectl proxy** 打開：

```shell
kubectl proxy
```

然後連到下面的網址，就能夠看到 Kubernetes Dashboard：

[http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/](http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/)

到這裡就已經可以看到登入畫面了，不過這裡要注意的是，如果你是使用 ssh 連到你的主機的話，要先在本地端設定好 **kubectl**，在本地端輸入 **kubectl proxy**

## 建立 RBAC

### Service Account

建立一個 service 的帳戶

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kube-system
```

### ClusterRoleBinding

將剛剛建立的 Service Account **admin-user** 與 ClusterRole 做綁定

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kube-system
```

這樣就這定好了

## Login with Token

生成 admin-user 的 token：

```shell
$ kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
Name:         admin-user-token-ctr9s
Namespace:    kube-system
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: admin-user
              kubernetes.io/service-account.uid: b2a411d9-64c9-11e9-a59d-1c872c43607f

Type:  kubernetes.io/service-account-token

Data
====
ca.crt:     1025 bytes
namespace:  11 bytes
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6IiJ9.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhZG1pbi11c2VyLXRva2VuLWN0cjlzIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImFkbWluLXVzZXIiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiJiMmE0MTFkOS02NGM5LTExZTktYTU5ZC0xYzg3MmM0MzYwN2YiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZS1zeXN0ZW06YWRtaW4tdXNlciJ9.wA-CiA6yqJnxDJgQEX0pes32RGm9I_eh0fOoT6oEuLN849FhuNHPJ-LquEm9TFOVs944NXner_fq8czncrQUSiCmErH5ms6mSmXT8Ql-PjDWUCSpwb6rrDd8GCFSLA4LYZbgNQMiD21q2KY8HN3djW0QK1kcl3Cc19lyeAYMQ6DL6WU1AWzlsZEzAkd_P6r8a6KXrDxZNRimIzl61yioVmLqPGN3imT0u3DUvedbDReWVSTBYvOEExvgchgKKuImr0i1V2m7GbfJ01kDwKcehh78YPPMtXG-wfwVLA9xancHtoP0exPQ15fx9fQvswEoH1Qwz5T4dA2TYXKzHweZvg
```

最後登入的時，使用 Token 登入，將 Token 複製貼上就能夠登入了。


## Trouble Shooting

### Address already in use

如果你是背景執行 proxy 指令的話：

```shell
kubectl proxy &
```

在這個時候，沒有關掉這個程式，直接關掉終端機，再次執行則會發現 port 已經被佔用了，顯示的錯誤如下：

```shell
F0422 16:14:59.688258   15922 proxy.go:158] listen tcp 127.0.0.1:8001: bind: address already in use
```

#### 方法一

如果你執行 proxy 的終端機還開著，可以使用 **fg** 指令把程式叫回，在按 **Ctrl-C** 即可：

#### 方法二

萬一你把執行 proxy 的終端機關閉了，則必須先找到執行 proxy 的執行緒 pid：

```shell
$ netstat -tulp | grep kubectl
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
tcp        0      0 localhost:8001          0.0.0.0:*               LISTEN      15908/kubectl
# 或是
$ ps -ef | grep kubectl
akiicat  15908  7633  0 16:14 pts/1    00:00:00 kubectl proxy
akiicat  20117  7633  0 16:23 pts/1    00:00:00 grep --color=auto kubectl
```

找到 pid，上面的例子的 pid 是 15908，然後程式強制刪除：

```shell
kill -9 <pid>
```

## Reference

- [Github Kubernetes Dashboard](https://github.com/kubernetes/dashboard)
- [How to stop kubectl proxy](https://stackoverflow.com/questions/46302126/how-to-stop-kubectl-proxy)
- [Creating sample user](https://github.com/kubernetes/dashboard/wiki/Creating-sample-user)
