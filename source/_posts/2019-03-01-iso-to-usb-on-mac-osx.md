---
title: Mac 將 iso 轉 usb、dmg 轉 usb、iso 轉 dmg
date: 2019-03-01 00:08:05
categories:
  - Terminal
tags:
  - Mac Osx
  - Diskutil
  - ISO
  - DMG
  - USB
---


## ISO to USB

### 列出外部磁碟

```shell
diskutil list external
```

```shell
$ diskutil list external
/dev/disk2 (external, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:     FDisk_partition_scheme                        *62.0 GB    disk2
   1:               Windows_NTFS Ubuntu1804              4.0 GB     disk2s1
   2:               Windows_NTFS Ubuntu1604              4.0 GB     disk2s2
   3:               Windows_NTFS Ubuntu1404              4.0 GB     disk2s3
   4:               Windows_NTFS Data                    50.0 GB    disk2s5
```

### unmount 磁碟

```shell
diskutil unmount disk2s1
```

```shell
$ diskutil unmount disk2s1
Volume Ubuntu1804 on disk2s1 unmounted
```

### 將 ISO 寫入磁碟

```shell
sudo dd if=[imagePath] of=/dev/r[diskID] bs=1m
```

- `if`：Input from，並非 Standard Input
- `of`：Output from

```shell
sudo dd if=~/Desktop/ubuntu-18.04.2-desktop-amd64.iso of=/dev/rdisk2s1 bs=1m
```

### 退出磁碟

```shell
diskutil eject disk2
```

## ISO to DMG

```shell
hdiutil convert -format UDRW -o /path/to/image.dmg /path/to/image.iso
```

## DMG to USB

跟 ISO to USB 同一條指令

```shell
sudo dd if=/path/to/image.dmg of=/dev/rdiskN
```