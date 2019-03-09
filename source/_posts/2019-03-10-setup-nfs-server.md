---
title: 在 ubuntu 上架設 NFS server
date: 2019-03-10 01:54:10
categories:
  - DevOps
tags:
  - NFS
---


##思路

[NFS (Network File System)](https://en.wikipedia.org/wiki/Network_File_System) 可以透過網路，讓不同的作業系統，分享個別的檔案。

而我想要在 Ubuntu 上建立 NFS Server，透過 Mac 上的 NFS Client 連上。目前的環境：

- Ubuntu 18.04: 192.168.0.10
- Mac OS X Mojave: 192.168.0.20

## 安裝 NFS Server

```shell
sudo apt install nfs-kernel-server
```

安裝好之後，會在 `/etc` 底下會多一個 `exports` 的檔案，待會我們可以修改這個檔案，來調整想要分享的檔案。

### 建立資料夾

我想要把 `/var/nfs` 這個資料夾分享出去，所以先建立這個資料夾：

```shell
sudo mkdir /var/nfs -p
```

### 資料夾權限

目前 `/var/nfs` 的權限是 `root:root` ，第一個 root 是 owner 的權限，第二個 root 是 group 的權限：

```shell
$ ls -la /var/nfs
drwxr-xr-x  3 root root     4096  3月  9 22:26 nfs
```

如果要把檔案分享出去的話，需要修改資料夾的權限為 `nobody:nogroup`，且加上 `-R` 的參數把資料夾底下的所有子資料夾都設為 `nobody:nogroup`：

```shell
sudo chown -R nobody:nogroup /var/nfs
```

否則在之後 mount 的時後會出現 `Operation not permitted` 的錯誤：

```shell
mount_nfs: can't mount /var/nfs from 192.168.0.10 onto ...: Operation not permitted
```

### 設定

修改 `/etc/exports`：

```shell
sudo vim /etc/exports
```

然後把想要分享的檔案加入：

```shell
# /etc/exports
# 格式：
# 在 ubuntu 上想要分享的檔案         這個 IP 連進來的可以存取這個檔案(選項)
/var/nfs 192.168.0.20/8(rw,sync,no_subtree_check)
```

至於選項是選擇性填寫的，有很多參數可以選：

- `ro`：read only
- `rw`：read and write
- `async`：此選項允許 NFS Server 違反 NFS protocol，允許檔案尚未存回磁碟之前回覆請求。這個選項可以提高性能，但是有可能會讓 server 崩潰，可能會需要重新啟動 server. 或檔案遺失。
- `sync`：只會儲存檔案會磁碟之後才會回覆請求
- `no_subtree_check`：禁用子樹檢查，會有些微的不安全，但在某些情況下可以提高可靠性。

更多參數的選項可以參考 [LInux exports](https://linux.die.net/man/5/exports)

### 重新啟動 Server

```shell
sudo systemctl restart nfs-kernel-server
```

### 檢查

輸入以下的指令，確認使否把檔案加入 NFS Server：

```shell
$ sudo exportfs 
/home/akiicat/share
		192.168.0.20/8
```

## 安裝 NFS Client

### Mac OS X

#### Mount

我想要跟 share 資料夾做分享，使用 mount 指令將遠端的資料夾 `/var/nfs` 掛載到本地端的資料夾 `share`：

```shell
mkdir -p ~/share
sudo mount -t nfs -o rw,resvport 192.168.0.10:/var/nfs ~/share
```

#### 檢查

輸入以下指令檢查是否掛載成功：

```shell
$ mount
...
192.168.0.10:/var/nfs on /Users/akii/share (nfs)
```

#### 測試

在 `share` 資料夾內建立 `test` 檔案：

```shell
echo "Hello World" > /tmp/test
```

#### Unmount

```shell
sudo umount 192.168.0.10:/var/nfs
```



