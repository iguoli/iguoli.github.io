---
title: OpenSSL 常用命令
date: 2018-07-27
modify_date: 2021-06-30
tags: Encryption OpenSSL
key: Openssl-Commands-2018-07-27
---

## [OpenSSL][0] 简介

**OpenSSL** 是一个实现了 **传输层安全 (Transport Layer Security, TLS v1)** 网络协议和相关加密标准的加密工具箱.  

`openssl` 是在 shell 中使用 **OpenSSL** 加密库中各种加密函数的命令行工具.

<!--more-->

```zsh
openssl <command> [command_opts] [command_args]

# 查看版本
openssl version -a

# 分别列出当前 openssl 工具中所有 standard commands, message digest commands 或 cipher commands 的名称
openssl list-standard-commands
openssl list-message-digest-commands
openssl list-cipher-commands

# 分别列出所有 cipher 和 message digest 的名称，有别名的话以 from => to 的形式显示
openssl list-cipher-algorithms
openssl list-message-digest-algorithms

# 列出所有支持的公钥算法
openssl list-public-key-algorithms

# 查看指定名称的 command 是否可用，命令不存在，输出 no-<command>，命令存在，输出 <command>
openssl no-<command>
```

### Pass Phrase Arguments

`openssl` 中有几个命令可以接受密码参数，例如 `pkcs12`。通常使用 `-passin` 和 `-passout` 做为输入和输出密码。密码可以有多种形式，其格式如下所述。

| Format          | Description                                                                                                                                                                                                                                                                                                                                 |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `pass:password` | the actual password is **password**. Since the password is visible to utilities (like 'ps' under Unix) this form should only be used where security is not important.                                                                                                                                                                       |
| `env:var`       | obtain the password from the environment variable **var**. Since the environment of other processes is visible on certain platforms (e.g. ps under certain Unix OSes) this option should be used with caution.                                                                                                                              |
| `file:pathname` | the first line of **pathname** is the password. If the same pathname argument is supplied to **-passin** and **-passout** arguments then the first line will be used for the input password and the next line for the output password. **pathname** need not refer to a regular file: it could for example refer to a device or named pipe. |
| `fd:number`     | read the password from the file descriptor **number**. This can be used to send the data via a pipe for example.                                                                                                                                                                                                                            |
| `stdin`         | read the password from standard input.                                                                                                                                                                                                                                                                                                      |

如果命令要求输入密码，但未在参数中提供密码，则命令会在终端提示用户输入密码。

## 证书与私钥

使用 HTTPS 方式访问 Web 服务，客户端需要信任由服务端生成的证书。

![https connection](/assets/images/openssl/https_connection.png)

## 编码格式

同样的证书或私钥，可能有不同的编码格式，目前有以下两种编码格式.

- **DER** - Distinguished Encoding Rules，二进制格式的证书或密钥，不可读.

- **PEM** - Privacy Enhanced Mail，BASE64 编码的 DER 证书或密钥，带有额外的页眉和页脚.

### 证书 (PEM 格式)  
证书扩展名一般为 ".pem"，".crt" 或 ".cer"  
以 `-----BEGIN CERTIFICATE-----`{:.info} 作为开头  
以 `-----END CERTIFICATE-----`{:.info} 作为结尾

```text
-----BEGIN CERTIFICATE-----
MIICfDCCAWQCCQC1WdJznfCazDANBgkqhkiG9w0BAQsFADAAMB4XDTE5MTIxNjEz
MDkzMFoXDTIwMTIxNTEzMDkzMFowADCCASIwDQYJKoZIhvcNAQEBBQADggEPADCC
AQoCggEBAJpj/KqgVcECEYGa05ZrxL0Zf7IP6goPwf9ePrrMIw5J/5KZVJN/d9nV
R/MOH/n7eOI3n3MrHcHRuo1Vww8ZNOkXO/7FMSzFIeWkygwM34nI7wXYSjEjV2DF
QPhItwkYWZJJToxGxLSgrWDm+VqoNEtXJLAtJ/tcj5GSknbQ6E+KKHUKon1UzDDe
wosEIVQqezRVZICIIt25wTL3cHzZNJP3smq990nJ764VOZ/oGCYgtZLMrM2UYan8
Z93hdz8opuD1kRlpb9ouPM2/f9k7ajOIdm+v2IOrofvQaPQ5KnWiai6FZXvxCaCp
T4uzXFiosZJLziK/0TLDcoUZQpXf/40CAwEAATANBgkqhkiG9w0BAQsFAAOCAQEA
RnVPDjk/oeTrHviM6p4RtRq6+tsKtQWFt7EnvWAlbnv05cSdTVCoiXUJVGQbMstV
a3KTOtnBBiTXSrsnNkTSeY+sKlW+Hwo20MAat5nhj0696RLXfNcHRHutHhAq8qJK
wkdE5mNiWxf7g+CbAEdbzbti94CNJ5b0VzOth5XY28o/fLsKGqspa7K3F5iPEbt6
RcJpaR10bcwTHEDs5ObMLdbigQWuJg6Ko8Rfbakpiq1ow+9RbZtpa1eOA5Z15Wh1
TxvUXvdxO8/78KLVk6lwhaNXqZTdzZqK7Oi7Bd3YhwwaauA98Rm3RJdYNkycjyde
1VYZpKI+qnyNS6tut8z+fg==
-----END CERTIFICATE-----
```

### 私钥(PEM 格式)  
私钥扩展名一般为 ".pem" 或 ".key"  
以 `-----BEGIN RSA PRIVATE KEY-----`{:.info} 作为开头  
以 `-----END RSA PRIVATE KEY-----`{:.info} 作为结尾

```text
-----BEGIN RSA PRIVATE KEY-----
MIIEowIBAAKCAQEAmmP8qqBVwQIRgZrTlmvEvRl/sg/qCg/B/14+uswjDkn/kplU
k3932dVH8w4f+ft44jefcysdwdG6jVXDDxk06Rc7/sUxLMUh5aTKDAzficjvBdhK
MSNXYMVA+Ei3CRhZkklOjEbEtKCtYOb5Wqg0S1cksC0n+1yPkZKSdtDoT4oodQqi
fVTMMN7CiwQhVCp7NFVkgIgi3bnBMvdwfNk0k/eyar33ScnvrhU5n+gYJiC1ksys
zZRhqfxn3eF3Pyim4PWRGWlv2i48zb9/2TtqM4h2b6/Yg6uh+9Bo9DkqdaJqLoVl
e/EJoKlPi7NcWKixkkvOIr/RMsNyhRlCld//jQIDAQABAoIBACzKVzIVI+vDstp7
1WitST1nGWHKNibHHYv9hotgz8bEdw3Lx/qmn1mmA6Ki9Wnrp81RP8QEvTROqSV5
vQYzVONOp0wpXzPS2RurbCaE0ZFCpv12G0gYLbBYaZneVqOf2dSTyeLuYQDaIJKd
T7gaVlmJJY6q+w90cf5UnIM15j+0rNruBRxu7RlbDlzaX1KXJA87CpuATozBg/hq
yC3g/K+WcTLfxp+xabd/8K7cGkEcQBkf0uHoUgD9gXvDt2TPfLtkb9yl/mmiGAh5
o21xQd+fYCsYBTpkPSqC9al+tOs/yQ1CbsTEjmLuytrajXbQ6mHSmQqb2zp8fTQx
3a1G0SECgYEAylotEQ9pr5nfoKj78ANhSV6GKNcOBf2XpJAaHEuzGYqyPYv25lMc
CW0bOyQO7hwcSpM5OnZshgVimcoT7FcN2zcycDdt9zqbRHqqoNHvhN6i/FPaoIuo
WmwgY3058yvbKBDj77oUv+ErMLYbYaLuDcalO1ChOTBLRmMQxJMmPCkCgYEAw1Kc
Lx6BI51PMiY0yNvTyE8i8aCGN3eNxwetKI7mVIx3p6rb45vjS0P08UOtaqb6sZ1M
whE0fSUnGENQ+wuO5+ZWK6c2OrSLgHFTwGjBrcPvPdXec2+SLxYTJPqiVRzTEAOL
IrE2y09fIMqdWO3l0VX8JF5yllzEFtmRAp7MlMUCgYB1LkNq3cIWLsWSBEnhdBOi
qVqHzAIS9ggbBFgairvszqTpf2Q7fWV/D+/1D6yVtzGq/hbmBC5UbknrRVrEQqWD
jTNoSLEluoqv3VlySCCskUFRXj39OuMDehpHgv1MFNmVGjLlYJ0jgQHB87NnlzLE
R4ToFIlI1O7ClBvhulUqAQKBgQCuRD5lK+o8RFYsgDoIG1nRFgovWvFk+53bPdYY
oaaPOORpC+A0KxmYRYF84M0Qovp5Dcc2PvN91HAVDbpqwd02uiOXvxB7QCpW5sW6
nbxvtKj3mqFmjNz4ddGO5K+BEL6fQwoYYdRpgN92+k940IiDRRw5tqq1zP6HBIaE
t/n/oQKBgGBlP2WkS0Z8eX+CI59sD/TL1IZYPwZIfgVQ604Ycim3BatehdDWezk2
gP+frXcbdgp6jgsLWqn6uRxI3Cp/3qJCWr3foeDsyyxEDJjJXi77heIwoyOsfDDQ
1qur+70lLbptxciDt6UJOLX4BkzvzdaagakB+hHJoClNTmDCUGMB
-----END RSA PRIVATE KEY-----
```

## [RSA](https://zh.wikipedia.org/wiki/RSA加密演算法)

**RSA** 是常用的非对称加密算法，用于生成公钥和私钥对.

`openssl genrsa` 命令可以生成 RSA 私钥.

- ***-aes256***  
  使用 aes256 算法加密私钥，并要求输入一个保护密码
  
- ***-out***  
  输出到指定文件，如果没有该选项，则输出到屏幕

- ***numbits***  
  生成密钥所需要的 bit 长度

```zsh
# 生成 2048 位不带密码保护的私钥
openssl genrsa -out private.key 2048

# 生成 4096 位带密码保护的私钥
openssl genrsa -aes256 -out private.key 4096
```

`openssl rsa` 命令处理 RSA 密钥. 可以转换不同格式的密钥并打印密钥组成内容

```zsh
# 打印密钥编码后版本
openssl rsa -in private.key

# 打印密钥纯文本版本
openssl rsa -in private.key -text -noout

# 读取私钥，输出公钥
openssl rsa -in private.key -pubout -out public.pem

# 删除私钥的保护密码
openssl rsa -in encrypted.key -out unencrypted.key

# 给私钥添加密码保护
openssl rsa -aes256 -in unencrypted.key -out encrypted.key

# 将私钥从 PEM 格式转换成 DER 格式
openssl rsa -in private.key -outform DER -out private.der
```

```zsh
# ssh 生成公钥的命令
ssh-keygen -y -f id_rsa > id_rsa.pub
```

## [PKCS - Public Key Cryptography Standards (公钥密码学标准)][5]

### PKCS#7 - 密码消息语法标准（Cryptographic Message Syntax Standard）

`openssl pkcs7` 命令用于处理 `PKCS#7(p7b)` 格式的证书

```zsh
# 将 p7b 格式的证书转换成 x509 格式的证书
openssl pkcs7 -print_certs -in cert.p7b -out cert.pem
```

### PKCS#10 - 证书申请标准（Certification Request Standard）

规范了向证书中心申请证书之CSR（certificate signing request）的格式。 在 PKI 系统中，一个 **证书签名请求 (CSR)** 是申请者为了向 CA 中心申请 **[数字身份证书][4]** 而发送的信息。下面是一个 CSR 中需要的典型信息。

|   DC   |       Information        |         Description          |           Sample           |
| :----: | :----------------------: | :--------------------------: | :------------------------: |
|  `CN`  |       Common Name        | 希望保证安全的域名 [FQDN][3] |      *.wikipedia.org       |
|  `O`   |    Organization Name     |        组织或公司名称        | Wikimedia Foundation, Inc. |
|  `OU`  | Organizational Unit Name |           部门名称           |             IT             |
|  `L`   |     Locality / City      |          城市或地区          |       San Francisco        |
|  `ST`  |    State or Province     |            省/州             |         California         |
|  `C`   |         Country          |       两字母的国家代码       |             US             |
| `MAIL` |      Email address       |     公司或部门的联系邮箱     |    support@it.corp.com     |

这些信息可以在自定义的配置文件中提前配置，例如创建一个 `openssl.cnf` 的配置文件，内容如下:

```conf
[ req ]
distinguished_name = req_distinguished_name
req_extensions     = req_ext

[ req_distinguished_name ]
countryName                     = Country Name (2 letter code)
countryName_default             = CN
stateOrProvinceName             = State or Province Name (full name)
stateOrProvinceName_default     = Bejing
localityName                    = Locality Name (eg, city)
localityName_default            = Chaoyang
organizationName                = Organization Name (eg, company)
organizationName_default        = Galaxy, LTD
organizationalUnitName          = Organizational Unit Name (eg, section)
organizationalUnitName_default  = Sales
commonName                      = Common Name (e.g. server FQDN or YOUR name)
commonName_max                  = 64
commonName_default              = localhost
emailAddress                    = Email Address
emailAddress_max                = 64
emailAddress_default            = sales@galaxy.com

[ req_ext ]
subjectAltName = @alt_names

[ alt_names ]
DNS.1   = www.galaxy.com
DNS.2   = galaxy.com
DNS.3   = *.galaxy.com
IP.1    = 10.10.10.10
```

其中 `[ req_ext ]` 中配置了 **Subject Alternative Name (SAN)**，可以在随后生成 CSR 或自签名证书时使用。

`openssl req` 命令通常用来创建和处理 `PKCS#10` 格式的证书，也可以创建自签名证书。

- ***-days \<n\>***  
  指定证书有效期，默认是30天，与 `-x509` 选项一起使用

- ***-newkey rsa:2048***  
  生成一个新的 CSR，同时生成一个 2048 位的 RSA 私钥

- ***-keyout \<keyfile\>***  
  新私钥要写入的文件

- ***-nodes***  
  不对新私钥加密

- ***-key \<keyfile\>***  
  读取指定的私钥文件.

- ***-text***  
  同时打印纯文本版本和编码版本信息

- ***-noout***  
  不打印编码后版本 (BASE64编码)

- ***-new***  
  生成一个新的证书请求，会提示用户输入相关字段的值，如果没有 ***-key*** 选项，会使用指定配置文件中的信息生成一个新的 RSA 私钥.

- ***-x509***  
  输出自签名的证书，而不是请求一个证书. 通常用于生成测试证书或自签名的根证书.

- ***-subj \<arg\>***  
  申请人信息，格式是 `/C=CN/O=Corp/.../CN=www.ez.com`，可以使用 `\` 转义，不会跳过空格.

- ***-[digets]***
  指定签署请求时使用的信息摘要算法，如 `-md5`，`-sha1`，`-sha256`

```zsh
# 生成一个 2048 位的无密码保护私钥和一个新的 CSR，使用 openssl.cnf 中的配置信息
openssl req -newkey rsa:2048 -nodes -keyout private.key -out cert.csr -config openssl.cnf

# 使用已有的私钥生成一个新的 CSR
openssl req -new -key private.key -out cert.csr

# 查看 CSR 证书
openssl req -in cert.csr -text -noout

# 生成一个新的自签名证书，并且使用 openssl.cnf 中的 [ req_ext ] 扩展来设置 Subject Alternative Name (SAN)
# 使用 openssl x509 也可以生成自签名证书，见后面 x509 部分
openssl req -new -x509 -days 365 -key private.key -sha256 -extensions req_ext -config openssl.cnf -out self-signed.crt
```

### PKCS#12 - 个人消息交换标准（Personal Information Exchange Syntax Standard）

[PKCS12][6] 是一种证书存储格式，用于实现将许多加密对象存储在一个单独的文件中。通常用它来打包一个私钥及有关的 X.509 证书，或者打包信任链的全部项目。

`openssl pkcs12` 命令用来解析或者创建 PKCS#12 格式的证书，参考 [pkcs12][7] 命令获得帮助。

- 创建 PKCS12 文件，包含私钥，client证书，其它证书，别名及文件保护密码

```zsh
openssl pkcs12 -export -inkey private.key -in cert.pem -certfile CACert.pem -out keystore.p12 -name entry_alias -passout pass:password
```

注意：PKCS12 文件中存储的私钥将使用与 PKCS12 文件相同的保护密码
{:.warning}

`.pfx` 和 `.p12` 都是 PKCS#12 文件。因为历史原因，**PFX** 是 Microsoft 常用扩展名，**P12** 是 Netscape 常用扩展名。这两个扩展名可以互换使用。
{:.info}

- 解析 PKCS12 文件

```zsh
openssl pkcs12 -in keystore.p12

# 解析 PKCS12 文件并输出到文件
openssl pkcs12 -in keystore.p12 -out keystore.pem
```

- 输出 PKCS12 文件中的所有证书到文件

```zsh
openssl pkcs12 -in keystore.p12 -nokeys -out cert.pem
```

- 输出 PKCS12 文件中的 client 证书到文件

```zsh
openssl pkcs12 -in keystore.p12 -nokeys -clcerts -out cert.pem
```

- 输出 PKCS12 文件中的 CA 证书到文件

```zsh
openssl pkcs12 -in keystore.p12 -nokeys -cacerts -out cert.pem
```

- 输出 PKCS12 文件中的私钥到文件，私钥无密码保护

```zsh
openssl pkcs12 -in keystore.p12 -nocerts -out private.key -nodes
```

- 查看 PKCS12 文件中的证书链和私钥

```zsh
openssl pkcs12 -in keystore.p12 -chain
```

- 查看 PKCS12 文件中的证书链和私钥的额外信息

```zsh
openssl pkcs12 -in keystore.p12 -info
```

### Java KeyStore (JKS)

**Java KeyStore** 是 Java 存储私钥和公钥信息的存储格式，从 JDK8 开始，Java 推荐使用 PKCS12 格式的密钥库。`keytool` 是 Java 密钥和证书管理工具，用来操作 **Java KeyStore** 文件。

`keytool` 命令中用到两种密码

- **storepass**: 密钥库的保护密码
- **keypass**: 密钥库中存储的私钥的保护密码

`keytool -importkeystore` 命令用于将源密钥库中的单个条目或所有条目导入到目标密钥库。

- 提供 **srcalias** 选项后，该命令会将别名标识的单个条目导入目标密钥库。
  - 如果目标别名未提供 **destalias**，则将 **srcalias** 用作目标别名。
  - 如果源条目受密码保护，则 **srckeypass** 将用于恢复该条目。
  - 如果未提供 **srckeypass**，则 `keytool` 将尝试使用 **srcstorepass** 恢复该条目。
  - 如果未提供 **srcstorepass** 或它不正确，则将提示用户输入密码。
  - 目标条目将使用 **destkeypass** 保护。如果未提供 **destkeypass**，则目标条目将保持源条目密码。
  - 如果未提供 **deststorepass** ，则将提示用户输入密码。

- 如果未提供 **srcalias** 选项，那么源密钥库中的所有条目都将导入到目标密钥库中。
  - 每个目标条目将存储在源条目的别名下。
  - 如果源条目受密码保护，则将使用 **srcstorepass** 恢复该条目。
  - 如果未提供 **srcstorepass** 或它不正确，则将提示用户输入密码。
  - 如果目标密钥库中不支持源密钥库条目类型，或者在将条目存储到目标密钥库中时发生错误，则会提示用户是跳过条目还是退出。
  - 目标条目将受到源条目密码的保护。

- 如果目标别名库中已经存在目标别名，则会提示用户覆盖该条目，或使用其他别名创建新条目。

```zsh
# 将 P12 文件转换为 Java KeyStore 文件，目标条目使用与源条目相同的别名
keytool -importkeystore -srckeystore keystore.p12 -srcalias entry_alias -srcstoretype pkcs12 -srcstorepass storepass -destkeystore keystore.jks -deststorepass storepass

# 将 Java KeyStore 文件转换为 P12 文件，目标条目使用与源条目不同的别名
keytool -importkeystore -srckeystore keystore.jks -srcalias entry_alias -srcstorepass storepass -destkeystore keystore.p12 -destalias other_alias_name -deststoretype pkcs12 -deststorepass storepass
```

注意: PKCS12 不支持密钥库和私钥使用不同的保护密码，所以转换到 PKCS12 格式的密钥库时，如果指定了 **destkeypass** 和 **deststorepass** ，那么密码必须相同。
{:.warning}

`keytool` 其它命令

```zsh
# 查看 keytool 帮助
keytool -help

# 查看 keytool 子命令帮助
keytool -exportcert -help

# 修改 keystore 保护密码
keytool -storepasswd -keystore keystore.jks -new new_storepass -storepass origin_storepass

# 修改 keypass 保护密码
keytool -keypasswd -keystore keystore.jks -storepass password -alias friendly_name -keypass origin_keypass -new new_keypass

# 修改 alias
keytool -changealias -keystore keystore.jks -storepass password -alias old_name -destalias new_name

# 查看证书文件
keytool -printcert -file cert.pem -v

# 打印 keystore 中指定 alias 的证书，以可读方式显示
keytool -list -keystore keystore.jks -storepass password -alias friendly_name -v

# 打印 keystore 中指定 alias 的证书，以 PEM 格式显示
keytool -list -keystore keystore.jks -storepass password -alias friendly_name -rfc

# 打印 jre 信任的证书
# 证书路径1: $JAVA_HOME/jre/lib/security/cacerts 
# 证书路径2: /usr/lib/jvm/java-1.8.0/jre/lib/security
# 证书路径3: /etc/pki/java/cacerts 
keytool -list -keystore /etc/pki/java/cacerts -storepass password

# 导入证书
keytool -importcert -trustcacerts -keystore keystore.jks -storepass password -alias friendly_name -file cert.pem

# 导出证书 PEM 格式
keytool -exportcert -keystore keystore.jks -storepass password -alias friendly_name -rfc -file cert.pem

# 删除指定证书
keytool -delete -keystore keystore.jks -alias friendly_name -storepass password
```

## X.509 证书

**[X.509][1]** 是[公钥证书 (Public Key Certificate)][2] 的标准格式，用来证明公开密钥持有者的身份。此文件包含了公钥信息、持有者身份信息（主体）、以及数字证书认证机构（发行者）对这份文件的数字签名，以保证这个文件的整体内容正确无误。

`openssl x509` 命令可以显示证书信息，转换证书格式 (PEM <-> DER), 像 ***mini CA*** 一样签署一个证书请求，或者编辑证书。

### 查看证书信息

- 查看证书完整信息

```zsh
openssl x509 -in cert.pem -noout -text
```

- 查看 DER 格式证书

```zsh
openssl x509 -in cert.der -inform der -text -noout
```

- 转换 DER 格式证书到 PEM 格式

```zsh
openssl x509 -inform DER -in cert.der -out cert.pem
```

- 查看证书日期信息

```zsh
openssl x509 -in cert.pem -noout -dates
```

- 查看证书过期时间

```zsh
openssl x509 -in cert.pem -noout -enddate
```

- 查看证书 subject

```zsh
openssl x509 -in cert.pem -noout -subject
```

- 查看证书发布者信息

```zsh
openssl x509 -in cert.pem -noout -issuer
```

- 查看证书 SAN (Subject Alternative Name)

```zsh
openssl x509 -in cert.pem -noout -ext subjectAltName
```

- 检查目录及子目录中所有 `.pem` 和 `.crt` 后辍的证书日期

```zsh
find . -regextype egrep -iregex '.*(pem|crt)' -print0 | xargs -0 -I% sh -c 'echo; echo %; openssl x509 -noout -subject -ext subjectAltName -enddate -in %'
```

### 生成自签名证书

1. [生成证书私钥](#rsa)

2. [生成CSR](#pkcs10---证书申请标准certification-request-standard)

3. 使用私钥和 CSR 生成自签名证书

- 方法一：私钥 -> CSR -> 自签名证书

```zsh
# 生成 2048 位不带密码保护的私钥
openssl genrsa -out private.key 2048

# 使用私钥生成CSR
openssl req -new -key private.key -out cert.csr

# 使用 openssl x509 命令生成自签名证书，有效期10年
openssl x509 -req -sha256 -days 3650 -in cert.csr -signkey private.key -out self-signed.crt
```

- 方法二：私钥和CSR -> 自签名证书

```zsh
# 生成 2048 位的无密码保护私钥和CSR，使用配置文件 openssl.cnf 设置证书信息
openssl req -newkey rsa:2048 -nodes -keyout private.key -out cert.csr -config openssl.cnf

# 使用 openssl x509 命令生成自签名证书，有效期10年
openssl x509 -req -sha256 -days 3650 -in cert.csr -signkey private.key -out self-signed.crt
```

- 方法三：私钥 -> 自签名证书

```zsh
# 生成 2048 位不带密码保护的私钥
openssl genrsa -out private.key 2048

# 使用 openssl req 命令生成自签名证书，使用配置文件 openssl.cnf 设置证书信息，使用 openssl.cnf 中的 [ req_ext ] 扩展来设置 Subject Alternative Name (SAN)
openssl req -new -x509 -days 365 -key private.key -sha256 -out self-signed.crt -extensions req_ext -config openssl.cnf
```

### PEM 证书长度

PEM 标准（RFC1421）强制证书每行使用64个字符，否则会遇到类似下面的 `bad base64 decode`{:.error} 报错
{:.warning}

```text
2675996:error:0906D064:PEM routines:PEM_read_bio:bad base64 decode:pem_lib.c:818:
```

可以使用 UNIX 工具 `fold` 将单行的 PEM 证书转换为标准的每行 64 字符的证书

```zsh
fold -w 64 oneline.pem > standard.pem
```

### 检查证书与私钥是否匹配

- 比较 CSR，证书和私钥中所包含公钥信息的 MD5 值，确保它们一致。

```zsh
openssl x509 -noout -modulus -in cert.pem | openssl md5

openssl req -noout -modulus -in csr.pem | openssl md5

openssl rsa -noout -modulus -in private.key | openssl md5
```

- 使用一条命令来比较证书与私钥是否匹配

```zsh
openssl x509 -noout -modulus -in cert.pem | openssl md5 ;\
openssl rsa -noout -modulus -in private.key | openssl md5
```

- 使用脚本检查

```zsh
#!/usr/bin/env bash

# script name: key_match

if [ "$#" -ne 2 ]; then
    echo "Usage: $0 <keyfile> <certfile or csrfile>"
    echo "Example: key_match private.key cert.pem"
    echo "Example: key_match private.key cert.csr"
    exit 1
fi

function match {
    keyfile=$1
    certfile=$2

    # Set color variables
    Red=$(tput setaf 1) Green=$(tput setaf 2) Yellow=$(tput setaf 3)
    R=$(tput rev) NC=$(tput sgr0)

    if grep -q '\-----BEGIN PRIVATE KEY-----' $keyfile 2>/dev/null; then
        key_hash=$(openssl rsa -modulus -noout -in $keyfile | openssl md5)
    else
        echo -e "Invalid key file: ${Red}$keyfile${NC}"
        return 1
    fi

    if grep -q '\-----BEGIN CERTIFICATE-----' $certfile 2>/dev/null; then
        cert_hash=$(openssl x509 -modulus -noout -in $certfile | openssl md5)
    elif grep -q '\-----BEGIN CERTIFICATE REQUEST-----' $certfile 2>/dev/null; then
        cert_hash=$(openssl req -modulus -noout -in $certfile | openssl md5)
    else
        echo -e "Invalid certificate or CSR file: ${Red}$certfile${NC}"
        return 1
    fi

    printf "%-30s md5 value: %s\n" "${Red}$keyfile${NC}" "${Yellow}$key_hash${NC}"
    printf "%-30s md5 value: %s\n" "${Red}$certfile${NC}" "${Yellow}$cert_hash${NC}"

    if [ "$key_hash" = "$cert_hash" ]; then
        echo -e "$R${Green}match.${NC}"
    else
        echo -e "$R${Red}not match!${NC}"
    fi
}

match "$@"
```

### 证书内容

```text
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            10:e6:fc:62:b7:41:8a:d5:00:5e:45:b6
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: C=BE, O=GlobalSign nv-sa, CN=GlobalSign Organization Validation CA - SHA256 - G2
        Validity
            Not Before: Nov 21 08:00:00 2016 GMT
            Not After : Nov 22 07:59:59 2017 GMT
        Subject: C=US, ST=California, L=San Francisco, O=Wikimedia Foundation, Inc., CN=*.wikipedia.org
        Subject Public Key Info:
            Public Key Algorithm: id-ecPublicKey
                Public-Key: (256 bit)
            pub:
                    00:c9:22:69:31:8a:d6:6c:ea:da:c3:7f:2c:ac:a5:
                    af:c0:02:ea:81:cb:65:b9:fd:0c:6d:46:5b:c9:1e:
                    9d:3b:ef
                ASN1 OID: prime256v1
                NIST CURVE: P-256
        X509v3 extensions:
            X509v3 Key Usage: critical
                Digital Signature, Key Agreement
            Authority Information Access:
                CA Issuers - URI:http://secure.globalsign.com/cacert/gsorganizationvalsha2g2r1.crt
                OCSP - URI:http://ocsp2.globalsign.com/gsorganizationvalsha2g2
            X509v3 Certificate Policies:
                Policy: 1.3.6.1.4.1.4146.1.20
                  CPS: https://www.globalsign.com/repository/
                Policy: 2.23.140.1.2.2
            X509v3 Basic Constraints:
                CA:FALSE
            X509v3 CRL Distribution Points:
                Full Name:
                  URI:http://crl.globalsign.com/gs/gsorganizationvalsha2g2.crl
            X509v3 Subject Alternative Name:
                DNS:wikipedia.org, DNS:*.mediawiki.org, DNS:wikimedia.org
            X509v3 Extended Key Usage:
                TLS Web Server Authentication, TLS Web Client Authentication
            X509v3 Subject Key Identifier:
                28:2A:26:2A:57:8B:3B:CE:B4:D6:AB:54:EF:D7:38:21:2C:49:5C:36
            X509v3 Authority Key Identifier:
                keyid:96:DE:61:F1:BD:1C:16:29:53:1C:C0:CC:7D:3B:83:00:40:E6:1A:7C

    Signature Algorithm: sha256WithRSAEncryption
         8b:c3:ed:d1:9d:39:6f:af:40:72:bd:1e:18:5e:30:54:23:35:
         ...
```

![Certificate](/assets/images/secure/x509.png)

## S_CLIENT

> s_client 是一个以 SSL 协议连接远程服务器的客户端程序，该工具可以用于测试诊断。

常用参数如下：

- ***-connect host:port***  
  指定远程服务器的地址和端口，如果没有该参数，默认值为 localhost:443

- ***-cert filename***  
  若服务器端需要验证客户端的身份，通过 -cert 指定客户端的证书文件。

- ***-key filename***  
  指定私钥文件。

- ***-verify depth***  
  打开服务器证书验证并定义证书验证过程中的最大深度。

- ***-servername host***  
  在与服务器的通信中包含 SNI (Server Name Indication) 信息来指明要通信的服务器。

- ***-showcerts***  
  显示服务器证书链，如果没有此选项，默认只显示服务器当前证书。

- ***-CAfile filename***  
  指定用于验证服务器证书的根证书。

- ***-state***  
  打印出 SSL 会话的状态。

### With SNI (Server Name Indication)

如果远程服务器使用的是 SNI（即在一个 IP 地址上共享多个 SSL 主机），则需要发送正确的主机名才能获得正确的证书。

```zsh
openssl s_client -showcerts -servername example.com -connect example.com:443 </dev/null
```

### Without SNI

- 如果远程服务器未使用 SNI，则可以跳过 `-servername` 参数：

```zsh
openssl s_client -showcerts -connect example.com:443 </dev/null
```

- 使用 HTTP 代理

```zsh
openssl s_client -proxy 10.1.1.8:8800 -showcerts -connect example.com:443 </dev/null
```

- 显示服务器证书的详细信息

```zsh
echo | openssl s_client -connect example.com:443 2>/dev/null | openssl x509 -noout -text
```

```zsh
openssl s_client -connect example.com:443 </dev/null 2>/dev/null | openssl x509 -noout -text
```

- 检查 PostgreSQL 数据库使用的证书（需要使用 `openssl 1.1.1` 之后的版本）

```zsh
echo | openssl s_client -starttls postgres -connect localhost:5432 2>/dev/null | openssl x509 -noout -text
```

- 显示服务器证书的所有者，SAN和过期时间

```zsh
echo | openssl s_client -connect example.com:443 2>/dev/null | openssl x509 -noout -subject -ext subjectAltName -enddate
```

- 显示证书的 PEM 格式部分

```zsh
# 单证书
echo | openssl s_client -connect example.com:443 | openssl x509

# 证书链
echo | openssl s_client -showcerts -connect example.com:443 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p'
```

## S_SERVER

| Item                            | Location               |
| ------------------------------- | ---------------------- |
| CA certificate (public key)     | ca_certificate.pem     |
| Server certificate (public key) | server_certificate.pem |
| Server private key              | server_key.pem         |
| Client certificate (public key) | client_certificate.pem |
| Client private key              | client_key.pem         |

```zsh
openssl s_server -accept 8443 \
  -cert server_certificate.pem -key server_key.pem -CAfile ca_certificate.pem
```

上面的命令将启动一个 OpenSSL s_server 进程，使用提供的CA证书，服务器证书和私钥。用于测试对TLS连接的证书进行完整性检查。

```zsh
openssl s_client -connect localhost:8443 \
  -cert client_certificate.pem -key client_key.pem -CAfile ca_certificate.pem \
  -verify 8 -verify_hostname CN_NAME
```

将 CN_NAME 替换为希望验证的主机域名或证书中的 CN 名称。这将打开与上面启动的示例TLS服务器的新TLS连接。 可以不使用-verify_hostname参数，但是OpenSSL将不再执行该验证。

[0]: https://www.openssl.org/docs/manmaster
[1]: https://zh.wikipedia.org/wiki/X.509
[2]: https://en.wikipedia.org/wiki/Public_key_certificate
[3]: https://en.wikipedia.org/wiki/Fully_qualified_domain_name
[4]: https://zh.wikipedia.org/wiki/公开密钥认证
[5]: https://zh.wikipedia.org/wiki/公钥密码学标准
[6]: https://zh.wikipedia.org/wiki/PKCS_12
[7]: https://www.openssl.org/docs/man1.1.1/man1/pkcs12.html