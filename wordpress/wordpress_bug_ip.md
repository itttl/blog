<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2018-08-22
title: [漏洞修复]wordpress wp_http_validate_url函数对输入IP验证不当，进行SSRF
tags: wordpress,漏洞修复
category: wordpress
status: publist
summary: wordpress wp_http_validate_url函数对输入IP验证不当，导致黑客可构造类似于012.10.10.10这样的畸形IP绕过验证，进行SSRF。
-->

### 介绍 ###
wordpress /wp-includes/http.php文件中的wp_http_validate_url函数对输入IP验证不当，导致黑客可构造类似于012.10.10.10这样的畸形IP绕过验证，进行SSRF。

### 修复 ###
#### 找到/wp-includes/http.php这个文件
```
## 533行左右，可搜索strtolower定位
$same_host = strtolower( $parsed_home['host'] ) === strtolower( $parsed_url['host'] );

改成
$same_host = ( strtolower( $parsed_home['host'] ) === strtolower( $parsed_url['host'] ) || 'localhost' === strtolower( $parsed_url['host'] ) );
```

```
## 550行左右，可搜索127定位
if ( 127 === $parts[0] || 10 === $parts[0] || 0 === $parts[0]
    改为
if ( 127 === $parts[0] || 10 === $parts[0] || 0 === $parts[0] || 0 === $parts[0]
```

[阿里云官方文档][100]

[100]:http://nginx.org/en/docs/http/ngx_http_core_module.html#satisfy
