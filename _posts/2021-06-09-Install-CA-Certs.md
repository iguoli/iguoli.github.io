---
title: Install CA Certs
date: 2021-06-09
modify_date: 2021-06-09
tags: SSL-TLS
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

Check the CA cert is installed into `ca-bundle.crt`

```zsh
head /etc/pki/tls/certs/ca-bundle.crt
```

## Install CA certs to JDK

```zsh
keytool -importcert -alias friendly_name -file ca.pem -storepass changeit
```
