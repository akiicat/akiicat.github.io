---
title: Linux 大量新增使用者
categories:
- Linux
tags:
- Linux
- Terminal
- Add User
---

## 建立腳本

下面這個腳本會建立五個使用者，分別是 user{1,2,3,4,5}，這五個使用者會在同一個群組 mygroup：

```shell
#!/bin/bash
groupadd mygroup
for username in user1 user2 user3 user4 user5
do
	useradd -G mygroup $username
	echo "password" | passwd --stdin $username
done
```

