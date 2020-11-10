---
title: "Gradle + GitHub Actions 自动发布项目至 Maven 仓库"
date: 2020-11-10T16:35:21+08:00
tags: ["Gradle","Maven","GitHub Actions","CI/CD"]
categories: ["CI/CD"]
draft: true
---

## 摘要
文章内容核心是使用 Gradle 整合 GitHub Actions 自动化发布 Java 项目至 Maven 仓库。文章内是将项目发布至 sonatype 提供的仓库中。如果你需要自动化发布，或者需要将项目同步至 Maven 中央仓库请仔细阅读。

## 前期准备
1. 可同步 Maven 中央仓库的门票，在 [Issues Sonatype](https://issues.sonatype.org/secure/Dashboard.jspa) 上申请；
2. OpenPGP 证书，需要同步至公共的服务器；
3. 一个 Java Project；
4. [GitHub](https://github.com/)。

---

关于同步门票部分可参考提供的[链接](https://central.sonatype.org/pages/producers.html)，文章内主要叙述证书相关的内容。

## OpenPGP 证书
因我使用的 Windows 操作系统这里使用 [Gpg4win](https://www.gpg4win.org/) 工具生成同步证书。如果你使用的是 Mac 或者 Linux 操作系统可使用其它 GPG 工具如 [GnuPG](https://www.gnupg.org/)。

### 安装 Gpg4win
下载地址 [Gpg4win](https://www.gpg4win.org/)。

### 生成证书
```shell
$ gpg --full-generate-key
```

运行上面的命令按照指示生成 RSA 的证书。

```
C:\Users\admin>gpg --full-generate-key
gpg (GnuPG) 2.2.23; Copyright (C) 2020 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

... 省略部分 ...

public and secret key created and signed.

pub   rsa3072 2020-11-10 [SC] [expires: 2021-11-10]
      449B75F00B2DA482AB8D03E8493DA88E2B89E19F
uid                      kk70-blog (blog) <kevinz@weghst.com>
sub   rsa3072 2020-11-10 [E] [expires: 2021-11-10]
```

### 浏览证书
可以使用 `gpg --list-secret-keys --keyid-format SHORT` 命令查询证书列表。
```
C:\Users\admin>gpg --list-secret-keys --keyid-format SHORT
C:/Users/admin/AppData/Roaming/gnupg/pubring.kbx
------------------------------------------------
sec   rsa3072/2B89E19F 2020-11-10 [SC] [expires: 2021-11-10]
      449B75F00B2DA482AB8D03E8493DA88E2B89E19F
uid         [ultimate] kk70-blog (blog) <kevinz@weghst.com>
ssb   rsa3072/6B7BF2DA 2020-11-10 [E] [expires: 2021-11-10]
```

### 发布证书
```shell
gpg --keyserver hkps.pool.sks-keyservers.net --send-keys 2B89E19F
```
使用 `gpg` 命令将证书发布到公共服务器 `2B89E19F` 替换为自己证书的 keyid。可在 [http://keys.gnupg.net/](http://keys.gnupg.net/) 网站搜索自己发布的证书。

## Gradle 项目配置


---

## 项目链接
[https://github.com/kevin70/chaos](https://github.com/kevin70/chaos)
