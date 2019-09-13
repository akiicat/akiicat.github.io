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





## Ubuntu

```shell
# -d :後面接日期，修改 shadow 第三欄位(最近一次更改密碼的日期)，格式 YYYY-MM-DD
# 設定為 0 則此時此帳號的密碼建立時間會被改為 1970/1/1 ，所以會有問題！#!/bin/bash
groupadd mygroup
for username in $(cat users.txt)
do

	# Ubuntu 在建立使用者的時候，不會自動建立家目錄 /home/username，需加上 -m 參數
    useradd -m -G mygroup $username
	
    echo $username:"password" | chpasswd
    
    # 強制使用者第一次登入的時候修改密碼
    # -d :後面接日期，修改 shadow 第三欄位(最近一次更改密碼的日期)，格式 YYYY-MM-DD
	# 設定為 0 則此時此帳號的密碼建立時間會被改為 1970/1/1 ，所以會有問題！
    chage -d 0 agetest
done
```

