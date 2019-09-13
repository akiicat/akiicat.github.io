---
title: kubernetes-using-helm-install-nginx-ingress-controller
tags:
  - Kubernetes
  - Installation
  - Helm
  - Nginx
  - Ingress
  - Load Balance
categories:
  - Kubernetes
date: 2019-07-05 01:56:59
---


## 環境

- Google Kubernetes Engine
- Kubernetes version 1.12.8
- Helm version 2.14.1

## 安裝 Nginx Ingress Controller

Nginx Ingress Controller 只支持 http Layer 7 的 LoadBalancing，不支援 Layer 4 / Layer 7 混合 LoadBalancing。

如果不想使用 Layer 7 的 LoadBalancing 的話，可以參考{% post_link metallb-installation-for-bare-metal 'MetalLB 在 Kubernetes Bare Metal 上安裝 Layer 2 Load Balancer' %}。

```shell
helm install --name my-nginx stable/nginx-ingress
```

## 解除安裝

**--purge** 參數可以徹底刪除，不留下任何記錄

```shell
helm delete --purge my-nginx
```

## 設定參數

Nginx Ingress Controller 有很多參數可以設定，可以參考[這裡](https://github.com/helm/charts/tree/master/stable/nginx-ingress#configuration)。

套用參數的方法有兩種：

### 方法一

使用 **--set** Flag，後面接參數，若有多個參數可以使用逗號隔開：

```shell
helm install stable/nginx-ingress --name my-nginx \
    --set controller.stats.enabled=true
    
helm install stable/nginx-ingress --name nginx-ingress --set "rbac.create=true,controller.service.externalIPs[0]=192.168.100.211,controller.service.externalIPs[1]=192.168.100.212,controller.service.externalIPs[2]=192.168.100.213" 
```

### 方法二

使用 yaml 檔

```yaml
# config.yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  annotations:
    kubernetes.io/ingress.class: nginx
  name: example
  namespace: foo
spec:
  rules:
    - host: www.example.com
      http:
        paths:
          - backend:
              serviceName: exampleService
              servicePort: 80
            path: /
  # This section is only required if TLS is to be enabled for the Ingress
  tls:
      - hosts:
          - www.example.com
        secretName: example-tls
```

```shell
helm install stable/nginx-ingress --name my-nginx -f config.yaml
```

或是參考 Nginx Ingress 提供預設的設定檔 [values.yaml](https://github.com/helm/charts/blob/master/stable/nginx-ingress/values.yaml) 做修改。

## 測試

最後的結果會看到：

```shell
$ kubectl get service
NAME                                     TYPE           CLUSTER-IP      EXTERNAL-IP    PORT(S)                      AGE
kubernetes                               ClusterIP      10.11.240.1     <none>         443/TCP                      6h35m
my-db-influxdb                           ClusterIP      10.11.249.197   <none>         8086/TCP,8088/TCP            23m
my-nginx-nginx-ingress-controller        LoadBalancer   10.11.254.24    23.236.50.24   80:31054/TCP,443:30789/TCP   5m21s
my-nginx-nginx-ingress-default-backend   ClusterIP      10.11.255.132   <none>         80/TCP                       5m21s
```

因為是在 GCP 上做測試的，直接套用了 LoadBalancer 的設定，所以輸入 **EXTERNAL-IP** 進行測試看看：

- **/healthz**：回傳 200
- **/**：回傳 404

> 因為沒有做任何設定，這裡只能獲得 Health Check 的路徑 **/healthz**，直接獲得 **/** 的位置會回傳 404 的錯誤。

```shell
$ curl -v http://23.236.50.24/
# 回傳 404

$ curl -v http://23.236.50.24/healthz
# 回傳 200
*   Trying 23.236.50.24...
* TCP_NODELAY set
* Connected to 23.236.50.24 (23.236.50.24) port 80 (#0)
> GET /healthz HTTP/1.1
> Host: 23.236.50.24
> User-Agent: curl/7.52.1
> Accept: */*
>
< HTTP/1.1 200 OK
< Server: nginx/1.15.10
< Date: Wed, 03 Jul 2019 15:47:45 GMT
< Content-Type: text/html
< Content-Length: 0
< Connection: keep-alive
<
* Curl_http_done: called premature == 0
* Connection #0 to host 23.236.50.24 left intact
```

## Reference

- [Helm chart nginx-ingress](https://github.com/helm/charts/tree/master/stable/nginx-ingress)
- [NGINX Ingress Controller Installation Guide](https://kubernetes.github.io/ingress-nginx/deploy/)