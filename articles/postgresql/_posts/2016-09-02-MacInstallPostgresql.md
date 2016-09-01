---
title:  "PostgreSQL 安裝"
date:   2016-09-02 00:41:17 +0800
---

## 更新 homebrew

```sh
brew update & brew upgrade
```

## 安裝 PostgreSQL

```sh
brew install postgresql
```

## 建立 PostgreSQL

```sh
initdb /usr/local/var/postgres
```

<!--excerpt-->

## 啟動 PostgreSQL

```sh
pg_ctl -D /usr/local/var/postgres -l /usr/local/var/postgres/server.log start
```

## 停止 PostgreSQL

```sh
pg_ctl -D /usr/local/var/postgres stop -s -m fast
```

## 自動啟動 PostgreSQL
PostgreSQL 的版本要自行更改，這裡使用的是 `9.5.1`

```sh
mkdir -p ~/Library/LaunchAgents
cp /usr/local/Cellar/postgresql/9.5.1/homebrew.mxcl.postgresql.plist ~/Library/LaunchAgents/
launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.postgresql.plist
```

## 停止自動啟動 PostgreSQL

```sh
launchctl unload -w ~/Library/LaunchAgents/homebrew.mxcl.postgresql.plist
```

## 創建使用者 或 直接使用

```sh
createuser -d -a -P postgres
```

## 創立一個 Database

```sh
createdb test_1
```

## 進入 PostgreSQL Console

```sh
psql test_1

# psql console 查看 PostgreSQL 的 Version
SELECT version();
```

## 連線到 PostgreSQL

```sh
postgres://username:password@host:port/dbname
```
