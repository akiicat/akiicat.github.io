---
title: 管理多個 Kubernetes Cluster：建立、切換、合併 context
tags:
  - Kubernetes
  - Kubectl
  - Context
categories:
  - Kubernetes
date: 2019-04-24 10:39:09
---


## 用途

要存取某個 Kubernetes 的 cluster，必須先設定好 Kubernetes 的 **context**，context 裡面會描述要如何存取到你的 Kubernetes 的 cluster。

當你今天有兩個 Kubernetes 的 cluster 的時候，分別是正式版的 cluster 跟測試用的 cluster，很頻繁在這兩個 cluster 作切換，這時候只需要分別對兩個不同的 context 就可以了，然後利用 kubectl 提供的指令，就能在不同的 cluster 作切換。

雖然目前還用不到兩個 cluster，但我還是建議先設定這個檔案，因為在還沒有設定這個檔案前，你可能會需要 ssh 遠端回你的主機才能使用 kubectl 指令，不過你可以在本地端設定這份檔案，就不需要遠端回你的主機了。

能夠有這些方便的功能，都是建立在你已經在**本地端**建立好你的設定檔。

## Context

在 Kubernetes 裡面，切換不同的 cluster 是以 context 為單位，一個 context 裡面必需要三個元件，分別是 **User**、**Server**、**Certification**。這三個東西說起來也很直觀，有個使用者 (**User**) 必須要有憑證 (**Certification**) 才能連到某個 Cluster (**Server**)。

底下是一個 Context 所包含的內容：

```
                                    +---------------+
                                +---+ Certification |
                  +---------+   |   +---------------+
              +---+ Cluster +---+
+---------+   |   +---------+   |   +--------+
| Context +---+                 +---+ Server |
+---------+   |   +------+          +--------+
              +---+ User |
                  +------+
```

Server 跟 Certification 會一起放在 Cluster 底下，這麼做的好處是，如果今天有兩個不同的使用者，想要連到同一個 Cluster，則不需要重新輸入一次 Certification 跟 Server。

通常設定檔會放在 `$HOME/.kube/` 底下，然後檔名命名為 `config`，不需要副檔名，當你使用 kubectl 指令的時候，預設就會使用這個設定檔。

這裡提供一個設定檔的範例，裡面的架構就如同上面的圖，可以對照著看，我也加了些註解：

```yaml
# $HOME/.kube/config
apiVersion: v1
kind: Config
preferences: {}
current-context: kubernetes-admin@kubernetes # 預設使用哪個 Context

clusters:
- cluster:
    certificate-authority: .minikube/ca.crt
    server: https://192.168.99.100:8443
  name: minikube         # Cluster 的名稱，可在 Context 使用
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS...
    server: https://17.82.125.39:6443
  name: kubernetes
  
contexts:
- context:
    cluster: minikube    # 選則某個 Cluseter
    user: minikube       # 選則某個 User
  name: minikube         # 這個 Context 的名稱
- context:
    cluster: kubernetes
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
- context:
    cluster: kubernetes
    user: kubernetes-admin
    namespace: kube-system  # 指定 namesapce，若不指定預設為 default
  name: kubernetes-system@kubernetes

users:
- name: kubernetes-admin
  user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0...
    client-key-data: LS0tLS1CRUdJTiBSU0EgUF...
- name: minikube         # User 的名稱，可在 Context 使用
  user:
    client-certificate: .minikube/client.crt
    client-key: .minikube/client.key
```

如果路徑正確，檔名正確，設定內容正確，就能看一下我們目前狀態：

```shell
$ kubectl config get-contexts
CURRENT   NAME                           CLUSTER      AUTHINFO           NAMESPACE
*         kubernetes-admin@kubernetes    kubernetes   kubernetes-admin   
          kubernetes-system@kubernetes   kubernetes   kubernetes-admin   kube-system
          minikube                       minikube     minikube
```

第二欄 **NAME** 就是 context 的名稱，透過 **use-context** 指令就能夠在不同的設定檔作切換：

```shell
kubectl config use-context <context-name>
```

像是這樣：

```shell
$ kubectl config use-context minikube
Switched to context "minikube".

$ kubectl config get-contexts
CURRENT   NAME                           CLUSTER      AUTHINFO           NAMESPACE
          kubernetes-admin@kubernetes    kubernetes   kubernetes-admin   
          kubernetes-system@kubernetes   kubernetes   kubernetes-admin   kube-system
*         minikube                       minikube     minikube
```

## 路徑

尋找設定檔的優先順序：

1. 優先使用環境變數 `$KUBECONFIG` 所設定的路徑
2. 如果沒有設定 `$KUBECONFIG` 環境變數，則使用 `${HOME}/.kube/config`

假設想要使用其他的設定檔 `/tmp/admin.conf`，那可以這樣輸入：

```shell
export KUBECONFIG=/tmp/admin.conf
```

這看起來並不是非常好用，如果你要切換某個 context 的時候，還需要設定 `KUBECONFIG` 的路徑，如果能把所有 context 整合在一起就會好用多了。

## Merge

Kubernetes 目前並沒有 `kubectl config merge` 的指令，不過在未來可能會有，有興趣的話可以參考這篇 [Github 上的討論](https://github.com/kubernetes/kubernetes/issues/46381)。

### 暫時性

目前解決方法是用冒號 **:** 將不同的檔案串在一起，放在越前面的檔案，順位越高：

```shell
export KUBECONFIG=${HOME}/.kube/config:/tmp/admin.conf
```

或是在每次輸入指令前都加上 `KUBECONFIG` 的設定：

```shell
KUBECONFIG=${HOME}/.kube/config:/tmp/admin.conf kubectl config get-contexts
```

### 永久性

直接寫進 bashrc，每當開啟終端機的時候都會套用環境變數：

```shell
# ~/.bashrc
export KUBECONFIG=${HOME}/.kube/config:/tmp/admin.conf
```

一勞永逸地做法，把新的檔案跟 `${HOME}/.kube/config` 合併：

```shell
KUBECONFIG=${HOME}/.kube/config:/tmp/admin.conf kubectl config view --flatten > mergedkub && mv mergedkub ${HOME}/.kube/config
```

另外還有腳本：

```shell
# ~/.bashrc
function kmerge() {
  KUBECONFIG=~/.kube/config:$1 kubectl config view --flatten > ~/.kube/mergedkub && mv ~/.kube/mergedkub ~/.kube/config
}
```

使用方法：

```shell
$ kmerge /tmp/admin.conf
```

## Reference

- [Configure Access to Multiple Clusters](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)
- [Organizing Cluster Access Using kubeconfig Files](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/)
- [Add `kubectl config merge` command](https://github.com/kubernetes/kubernetes/issues/46381)