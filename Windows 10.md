# Proxy and SSL certificate settings for Anaconda (Windows 10)

本 memo は、Windows **10**にAnaconda を利用する際に必要となる、Proxy server と SSL 認証の設定をまとめたものです。
本設定を行うことで、conda, pip で各種 library を download することが可能となります。

## 1. SSL 証明書の保存
SSL証明書（ZscalerRootCertificate-2048-SHA256.crt）を、Anaconda の folder に保存する。
例として下記の場所が該当するが、Anaconda install 時に「Just Me / All Users」のどちらを選択したかにより保存先が異なることに注意。

- Just Me:	`C:\Users\ab070\AppData\Local\Continuum\anaconda3`
- All Users:	`C:\ProgramData\Anaconda3`

## 2. conda: proxy setting
### 2.1. conda configuration file の作成
```.condarc```を作成し、以下の様に proxy server と SSL 証明書 の path の記述を含める。
SSL証明書の path は、1. において保存した場所により異なることに注意。

- Just Me:	`C:\Users\ab070\AppData\Local\Continuum\anaconda3\ZscalerRootCertificate-2048-SHA256.crt`
- All Users:	`C:\ProgramData\Anaconda3\ZscalerRootCertificate-2048-SHA256.crt`


```Shell
# proxy_servers (map: NoneType, str)
#   A mapping to enable proxy settings. Keys can be either (1) a
#   scheme://hostname form, which will match any request to the given
#   scheme and exact hostname, or (2) just a scheme, which will match
#   requests to that scheme. Values are are the actual proxy server, and
#   are of the form 'scheme://[user:password@]host[:port]'. The optional
#   'user:password' inclusion enables HTTP Basic Auth with your proxy.
# 
# proxy_servers: {}
proxy_servers:
    http: http://zproxy.srv.chiyoda.local:80
    https: https://zproxy.srv.chiyoda.local:80
```
```Shell
# ssl_verify (bool, str)
#   aliases: verify_ssl
#   Conda verifies SSL certificates for HTTPS requests, just like a web
#   browser. By default, SSL verification is enabled, and conda operations
#   will fail if a required url's certificate cannot be verified. Setting
#   ssl_verify to False disables certification verificaiton. The value for
#   ssl_verify can also be (1) a path to a CA bundle file, or (2) a path
#   to a directory containing certificates of trusted CA.
# 
# ssl_verify: true
ssl_verify: C:\ProgramData\Anaconda3\ZscalerRootCertificate-2048-SHA256.crt # All Users の場合
```

### 2.2. ```.condarc``` の保存
作成した ```.condarc``` を保存する。
例として下記の場所が該当するが、Anaconda install 時に「Just Me / All Users」のどちらを選択したかにより保存先が異なることに注意。

- Just Me:	```C:\Users\[Man No.]```
- All Users:	```C:\ProgramData\Anaconda3```

## 3. pip: proxy setting
> Reference: https://pip.pypa.io/en/stable/user_guide/#configuration

### 3.1. pip configuration file の作成
```pip.ini```を作成し、以下の様に proxy server と 信頼できるものとする file download 元 の host nameの2点の記述を含める。

```Shell
[global]
proxy = http://zproxy.srv.chiyoda.local:80
        https://zproxy.srv.chiyoda.local:80
trusted-host = pypi.python.org
               pypi.org
               files.pythonhosted.org
```

### 3.2. ```pip.ini``` の保存
下記場所（pip folder が無い場合は作成して）に保存。

```C:\ProgramData\pip```


ユーザー毎に設定したい場合は、以下に保存する。(UsersフォルダはDドライブの場合もある)

```C:\Users\ユーザー名\AppData\Roaming\pip```

## NOTE
- Anaconda Prompt 起動時に、右 click から「管理者権限として実行」とすることに注意。詳細は不明だが、管理者権限で実行しないと Proxy server の経由、もしくは SSL 認証を実行できない。
- pip の SSL 証明書認証の設定が上手くいかないため、主要な 3 hosts を trusted-host として設定した。
