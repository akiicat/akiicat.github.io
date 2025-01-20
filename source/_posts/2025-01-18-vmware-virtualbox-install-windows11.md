---
title: 安裝 Windows 11 虛擬機過程與問題
tags:
  - Windows 11
  - VirtualBox
  - VMWare
categories:
  - VM
date: 2025-01-18 09:38:14
---

很久沒安裝 Windows 11 VM 了，原本想說應該很順利，結果裝了一整個晚上，沒想到會踩了一堆雷，所以這篇來紀錄一下安裝 Windows 11 VM 的過程：

## 讀取 ISO

在 Windows 11 開機的時候，若出現以下提示文字：

> Press any key to boot from CD or DVD...

此時，一定要按下**鍵盤**上的任意鍵，如果只用滑鼠輸入會失敗。

## 繞過 TPM 檢查

在安裝 Windows 11 時，系統會檢查硬體是否符合規格。如果是在虛擬機上安裝，得需要手動關閉 TPM 的檢查：

1. **啟動命令提示字元**：在安裝畫面按下 `Shift + F10`，這將開啟命令提示字元 (cmd)。
2. **開啟登錄編輯器**：在命令提示字元中輸入 `regedit` 並按下 Enter 鍵。
3. **新增 LabConfig 機碼**：
  - 找到 `HKEY_LOCAL_MACHINE\SYSTEM\Setup`。
  - 在此路徑下新增一個名為 `LabConfig` 的資料夾（機碼）。
4. **繞過檢查的設定**：在 `HKEY_LOCAL_MACHINE\SYSTEM\Setup\LabConfig` 機碼內新增以下三個 DWORD 32-bit 項目
  - `BypassTPMCheck`，值設為 1。
  - `BypassRAMCheck`，值設為 1。
  - `BypassSecureBootCheck`，值設為 1。

完成這些設定後，關閉登錄編輯器，繼續安裝，即可成功繞過檢查。

## 帳號登入

在安裝過程的最後階段，Windows 可能會要求輸入帳號密碼，目前還不知道要略過此步驟。

## 建議

安裝完成後，建議馬上建立一個快照 (snapshot)，這樣如果日後需要重置或修復系統，就不必重新安裝。

## Reference

- [Install Microsoft Windows 11 on VirtualBox][1]
- [Clean Install Windows 11][2]

[1]: https://blogs.oracle.com/virtualization/post/install-microsoft-windows-11-on-virtualbox
[2]: https://www.elevenforum.com/t/clean-install-windows-11.99/#step16
