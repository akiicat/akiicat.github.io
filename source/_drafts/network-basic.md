---
title: network-basic
tags:
---

### 網路介面卡路徑

#### Ubuntu/Debian

/etc/network/interfaces

/etc/NetworkManager/system-connections



```shell
# ubuntu
# /etc/NetworkManager/system-connections/Wired connection 1
[connection]
id=Wired connection 1
uuid=24146aba-941f-39de-9791-72e157b1b1f6
type=ethernet
autoconnect-priority=-999
permissions=
timestamp=1552410151

[ethernet]
mac-address=50:46:5D:53:79:26
mac-address-blacklist=

[ipv4]
address1=140.113.213.39/27,140.113.213.33
dns=8.8.8.8;
dns-search=
method=manual

[ipv6]
addr-gen-mode=stable-privacy
dns-search=
method=auto
```



#### Red Hat/Fedora

/etc/sysconfig/network-scripts





iwconfig # 無線網路設定

ifconfig # 有線網路設定