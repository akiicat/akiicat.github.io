---
title: Linux CentOS 大量新增使用者
categories:
- Linux
tags:
- Linux
- RHEL
- Fedora
- CentOS
- Red Hat
- Terminal
- Add User
---

## 基本環境

CentOS、Fedora、RHEL 同屬一個架構，照理來說都可以使用以下的指令

## 建立腳本

下面這個腳本會建立五個使用者，分別是 user{1,2,3,4,5}，這五個使用者會在同一個群組 mygroup：

```shell
#!/bin/bash
groupadd mygroup
for username in user1 user2 user3 user4 user5
do
	# 建立使用者以及他的群組
	useradd -G mygroup $username
	
	# 設定預設密碼 CentOS only
	# echo "password" | passwd --stdin $username
	
	# CentOS and Ubuntu
	echo $username:"password" | chpasswd
done
```

<!-- more -->

使用者在第一次登入時， 強制她們一定要更改密碼後才能夠使用系統資源

```shell
#!/bin/bash
groupadd mygroup
for username in user1 user2 user3 user4 user5
do
	useradd -G mygroup $username
	echo "password" | passwd --stdin $username
	
	# 強制使用者第一次登入的時候修改密碼
	# -d :後面接日期，修改 shadow 第三欄位(最近一次更改密碼的日期)，格式 YYYY-MM-DD
	# 設定為 0 則此時此帳號的密碼建立時間會被改為 1970/1/1 ，所以會有問題！
	chage -d 0 $username
done
```

## 首次登入強制修改密碼

強制使用者第一次登入的時候修改密碼

```shell
#!/bin/bash
groupadd mygroup
for username in user1 user2 user3 user4 user5
do
	useradd -G mygroup $username
	echo "password" | passwd --stdin $username
	
	# -d :後面接日期，修改 shadow 第三欄位(最近一次更改密碼的日期)，格式 YYYY-MM-DD
	# 設定為 0 則此時此帳號的密碼建立時間會被改為 1970/1/1 ，所以會有問題！
	chage -d 0 $username
done
```

## 從檔案建立使用者

先建立一個檔案，裡面存放者使用者清單：

```txt
# users.txt
user1
user2
user3
user4
user5
```

修改腳本的 for 迴圈，將它修改成由檔案讀入：

```shell
#!/bin/bash
groupadd mygroup
for username in $(cat users.txt)
do
    useradd -G mygroup $username
	echo "password" | passwd --stdin $username
	chage -d 0 $username
done
```



