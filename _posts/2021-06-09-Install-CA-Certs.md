---
title: Install CA Certs
date: 2021-06-09
modify_date: 2021-06-09
tags: Linux
key: Install-CA-Certs-2021-06-09
---

## Install CA certs on RedHat/CentOS 6/7/8

Install the `ca-certificates` package

```zsh
yum install ca-certificates
```

<!--more-->

Enable the dynamic CA configuration feature

```zsh
update-ca-trust force-enable
```

Copy ca file to `/etc/pki/ca-trust/source/anchors/`

```zsh
cp ca.pem /etc/pki/ca-trust/source/anchors/
```

Run command

```zsh
update-ca-trust extract
```

## Install CA certs for JDK

```zsh
keytool -import -keystore /etc/pki/java/cacerts -storepass changeit -file ca.pem -alias friendly_name
```
