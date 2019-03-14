---
title: 使用指令分割磁碟 on Mac
date: 2019-02-28 23:31:08
categories:
  - Mac OS X
tags:
  - Mac OS X
  - Terminal
  - Diskutil
  - Disk Partition
---


## 找出外接硬碟的 ID

```shell
diskutil list external
```

```shell
$ diskutil list external
/dev/disk2 (external, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:     FDisk_partition_scheme                        *62.0 GB    disk2
   1:                  Apple_HFS PATRIOT                 61.9 GB    disk2s1
```

分別會列出以下的資訊

- `TYPE`：硬碟的格式
- `NAME`：名稱
- `SIZE`：容量
- `IDENTIFIER`：辨識標籤

<!-- more -->

## 格式化硬碟

```shell
$ diskutil partitionDisk
Usage:  diskutil partitionDisk MountPoint|DiskIdentifier|DeviceNode
        [numberOfPartitions] [APM[Format]|MBR[Format]|GPT[Format]]
        [part1Format part1Name part1Size part2Format part2Name part2Size
         part3Format part3Name part3Size ...]
Usage:  diskutil partitionDisk 硬碟辨識標籤　[分區數量]　[硬碟分區架構(APM|MBR|GPT)]
        [第一個分區的格式　第一個分區的名稱　第一個分區的容量　
         第二個分區的格式　第二個分區的名稱　第二個分區的容量 ...]
```

- 硬碟辨識標籤：外接磁碟的 ID
- 分區數量：要分割幾個磁碟
- 硬碟分區架構：APM、MBR、GPT
- 分區的格式：可以使用這個指令 `diskutil listFilesystems` 查看現在可用的格式
- 分區的名稱：自己取
- 分區的容量：用百分比表示，或使用 `B`、`K`、`M`、`G`、`T`、`P` 表示，`0` 代表剩餘容量 

```shell
diskutil partitionDisk disk2 2 GPT \
  JHFS+ "Time Machine" 50% \
  ExFAT "Data" 50%
```

如果只想要分割成一個區塊

```
diskutil partitionDisk disk2 1 MBR JHFS+ Data 0
```

## 分割現有磁碟

參數格式跟格式化硬碟硬碟相同

```shell
Usage:  diskutil splitPartition MountPoint|DiskIdentifier|DeviceNode
        [numberOfPartitions] [(<fileSystemPersonality> <volumeName> <size>)*]
```

分區大小：用百分比表示，或使用 `B`、`K`、`M`、`G`、`T`、`P` 表示，`R` 代表剩餘容量 

```shell
diskutil splitPartition disk2s2 2 JHFS+ "Time2" 4G ExFAT "Data" R
```

## 調整磁碟大小

需要在 GPT. 格式下才能調整大小

```shell
Usage:  diskutil resizeVolume MountPoint|DiskIdentifier|DeviceNode
        <newSize>|R [(<fileSysPersonality> <volName> <size>)*]
        diskutil resizeVolume MountPoint|DiskIdentifier|DeviceNode
        limits|mapsize [-plist]
```

```shell
diskutil resizeVolume disk2s2 6.1GiB
```

## 合併磁碟

```shell
Usage:  diskutil mergePartitions [force] format name
        DiskIdentifier|DeviceNode DiskIdentifier|DeviceNode
```

```shell
diskutil mergePartitions JHFS+ NewName disk2s2 disk2s4
```

## 重新命名磁碟

```shell
Usage:  diskutil rename[Volume] MountPoint|DiskIdentifier|DeviceNode newName
Usage:  diskutil rename 硬碟辨識標籤 新的磁碟名稱
```

```shell
diskutil rename disk2s1 Time1
```

