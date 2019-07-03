---
title: 在 kubernetes 上建立 influxdb 1.7.7
tags:
  - Kubernetes
  - Influxdb
  - Installation
categories:
  - Kubernetes
date: 2019-07-03 01:32:39
---

## 環境

- Google Kubernetes Engine
- Kubernetes 版本為 1.12.8

## 建立 Secret

[Kubernetes secret](https://kubernetes.io/docs/concepts/configuration/secret/) 可以存放重要訊息，像是使用者密碼之類，不適合暴露在外。

設定以下參數，稍後提供給 influxdb 使用：

1. **INFLUXDB_DATABASE**：資料庫名稱
2. **INFLUXDB_HOST**：主機名稱
3. **INFLUXDB_USERNAME**：使用者名稱
4. **INFLUXDB_PASSWORD**：使用者密碼

```shell
kubectl create secret generic influxdb-creds \
  --from-literal=INFLUXDB_DATABASE=db \
  --from-literal=INFLUXDB_USERNAME=root \
  --from-literal=INFLUXDB_PASSWORD=root \
  --from-literal=INFLUXDB_HOST=influxdb
```

輸入以下指令檢查參數是否設定完成：

```shell
$ kubectl get secrets
NAME                  TYPE                                  DATA   AGE
influxdb-creds        Opaque                                4      46m

$ kubectl describe secrets influxdb-creds
Name:         influxdb-creds
Namespace:    default
Labels:       <none>
Annotations:  <none>
Type:  Opaque
Data
====
INFLUXDB_DATABASE:  12 bytes
INFLUXDB_HOST:      8 bytes
INFLUXDB_PASSWORD:  4 bytes
INFLUXDB_USERNAME:  4 bytes
```

密文型態為 **Opaque**，裡面有四筆資料

## 建立儲存空間 Persistent Volume Claim (PVC)

使用 [Kubernetes PVC](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims) 宣告儲存空間，將儲存空間命名為 **influxdb**，容量大小 2GB：

```yaml
# pvc.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  labels:
    app: influxdb
  name: influxdb
spec:
  accessModes:
  - ReadWriteOnce
      resources:
    requests:
      storage: 2Gi
```

套用設定：

```shell
kubectl apply -f pvc.yaml
```

## 建立 Deployment

直接使用 Docker Hub 上的 [influxdb](https://hub.docker.com/_/influxdb)，版本為 1.7.7

```shell
kubectl run influx-deployment --image=influxdb:1.7.7
```

### 套用 Secret

套用先前設定的 influxdb-creds Secret，輸入以下指令進入修改模式：

```shell
kubectl edit deployment influxdb
```

在 **spec.template.spec.containers** 位置加入 kubernetes secret：

```yaml
spec:
  template:
    spec:
      containers:
      - name: influxdb
        envFrom:
        - secretRef:
            name: influxdb-creds
```

### 套用 Persistent Volume Claim (PVC)

套用先前設定的 influxdb PVC，輸入以下指令進入修改模式：

```shell
kubectl edit deployment influxdb
```

在 **spec.template.spec.volumes** 位置加入 Persistent Volume Claim (PVC)：

```yaml
spec:
  template:
    spec:
      volumes:
      - name: var-lib-influxdb
        persistentVolumeClaim:
          claimName: influxdb
```

並且在 **spec.template.spec.containers.volumeMounts** 位置加入 Persistent Volume (PV)：

```yaml
spec:
  template:
    spec:
      containers:
        volumeMounts:
        - mountPath: /var/lib/influxdb
          name: var-lib-influxdb
```

最後整份文件的結果會長得如下：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "4"
  creationTimestamp: 2019-07-02T15:59:33Z
  generation: 4
  labels:
    run: influxdb
  name: influxdb
  namespace: default
  resourceVersion: "15136"
  selfLink: /apis/apps/v1/namespaces/default/deployments/influxdb
  uid: 62295880-9ce2-11e9-8f7f-42010a8000e8
spec:
  progressDeadlineSeconds: 600
  replicas: 1
  revisionHistoryLimit: 2
  selector:
    matchLabels:
      run: influxdb
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        run: influxdb
    spec:
      containers:
      - envFrom:
        - secretRef:
            name: influxdb-creds
        image: docker.io/influxdb:1.7.7
        imagePullPolicy: IfNotPresent
        name: influxdb
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
        volumeMounts:
        - mountPath: /var/lib/influxdb
          name: var-lib-influxdb
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
      volumes:
      - name: var-lib-influxdb
        persistentVolumeClaim:
          claimName: influxdb
status:
  ...
```

## Expose Service

這裡使用 **LoadBalancer** 來 expose 我們的服務，需要有雲端平台的支援：

```shell
kubectl expose deployment influx-deployment --name=influx-service --type=LoadBalancer --port=8086 --target-port=8086
```

若 **EXTERNAL-IP** 顯示為 **pending**，則再過一陣再重輸入一次。

```shell
$ kubectl get service
NAME             TYPE           CLUSTER-IP      EXTERNAL-IP    PORT(S)          AGE
influxdb         LoadBalancer   10.11.255.248   34.68.51.223   8086:31365/TCP   69m
```

看到 IP address 就代表完成了，接下來就測試看看是否能連進 server。

## 測試

從是否能從外部連進來：

```shell
$ influx --version
InfluxDB shell version: 1.7.7

$ influx -host '34.68.51.223' -port '8086' -username 'root' -password 'root'
Connected to http://34.68.51.223:8086 version 1.7.7
InfluxDB shell version: 1.7.7
>
```

## Reference

- [Deploy InfluxDB and Grafana on Kubernetes to collect Twitter stats](https://opensource.com/article/19/2/deploy-influxdb-grafana-kubernetes)