---
title: Install CA Certs
date: 2021-06-09
modify_date: 2021-06-09
tags: SSL-TLS
key: Install-CA-Certs-2021-06-09
---

## Install CA certs on RedHat/CentOS 6/7/8

Install the `ca-certificates` package

```bash
yum install ca-certificates
```

<!--more-->

Enable the dynamic CA configuration feature

```bash
update-ca-trust force-enable
```

Copy ca file to `/etc/pki/ca-trust/source/anchors/`

```bash
cp ca.pem /etc/pki/ca-trust/source/anchors/
```

Run command

```bash
update-ca-trust extract
```

Check the CA cert is installed into `ca-bundle.crt`

```bash
head /etc/pki/tls/certs/ca-bundle.crt
```

## Install CA certs to JDK

```bash
keytool -importcert -alias friendly_name -file ca.pem -storepass changeit
```
