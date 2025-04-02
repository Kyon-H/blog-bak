---
layout: post
title: "SSL证书申请与安装"
subtitle: "SSL证书申请与安装"
date: "2025-04-02 23:00"
author: Kyon-H
header-img: img/post-bg-2015.jpg
tags:
---
## 一、dynv6域名申请证书

### 1. 获取 HTTP Tokens

点击dynv6网站右上角`账户`->`keys`，找到`HTTP Tokens`点击`Add HTTP Tokens`，自定义`name`并选择`zone`生成TOKEN。
### 2. 添加 SSH Public Keys

本地主机生成ssh密钥对
```shell
# -t 加密算法
# -C comment 注释
# -f 文件名
# 生成 ssh_dynv6 密钥，ssh_dynv6.pub 公钥
ssh-keygen -t ed25519 -C "example.v6.army" -f ssh_dynv6
```

将生成的公钥填入网站的 `SSH Public Keys`
### 3. 安装acme.sh

[acmesh-official/acme.sh](https://github.com/acmesh-official/acme.sh)

```shell
curl  https://get.acme.sh | sh sh -s email=my@example.com
```
### 4. 申请证书

#### 多域名申请证书

同一个 DNS 服务商通过 API 签发证书，只能保存一个域名的 Token 信息，导致后续证书更新失败。可以为每一个域名创建单独目录分别保存配置，申请和更新证书时`--config-home`指定配置文件目录。
```shell
# ssh私钥文件
export KEY="/path/ssh_dynv6"
# http token
export DYNV6_TOKEN="rPSc75..."
# 更改默认ca 更改配置文件位置
/home/user/.acme.sh/acme.sh --config-home /user/acme/example --set-default-ca --server letsencrypt
# 申请证书
/home/user/.acme.sh/acme.sh --config-home /user/acme/example --issue --dns dns_dynv6 -d 'example.v6.army,*.example.v6.army'
```
**提示：** 申请证书时会根据第一个域名生成子文件夹名，因此建议`*.example.v6.army`放后面。

申请的证书位于`/user/acme/example/example.v6.army_ecc/`目录下，公钥使用`fullchain.cer`，密钥使用`example.v6.army.key`。
### 5. 更新证书

创建脚本并设置自动化任务每周执行一次
```bash
#!/bin/bash
# 执行更新证书命令
output=$(/home/user/.acme.sh/acme.sh --cron --config-home /user/acme/example)
# 检查是否更新
if [[ "$output" =~ "Skipping. Next renewal time is" ]]; then
	echo "$output"
	exit
fi
# else
# 推送证书 upcert为自定义脚本
/user/acme/upcert -h example.v6.army -p /user/acme/example/example.v6.army_ecc
```
### 6. 推送证书

以safeline-tengine为例，向其更新证书
#### API方式

#### 本地方式

## 二、cloudflare域名申请证书