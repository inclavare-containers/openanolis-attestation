# OAAS Token

本文介绍OAAS返回的Attestation Token内容格式。

OAAS Token采取Json Web Key (JWT, RFC7519)标准令牌格式，签名算法采用RS384. 令牌结构按以下方式组织：

```
<Base64-Encoded JSON Header>.<Base64-Encoded JSON Payload>.<Base64-Encoded RS384 Signature>
```

## Header

Header的JSON结构内容字段如下：

| 名称 | 类型 | 示例值 | 描述 |
| :-----| :---- | :-----| :---- |
| alg | 字符串 | RS384 | RFC7519标准Header字段，指明令牌所使用的签名算法，当前固定为RS384算法 |
| typ | 字符串 | JWT | RFC7519标准Header字段，指明令牌类型，固定为JWT |

## Payload

Payload的JSON结构字段内容如下：

| 名称 | 类型 | 示例值 | 描述 |
| :-----| :---- | :-----| :---- |
| exp | Unix时间戳 | 1700796947 | RFC7519标准字段，指明令牌过期时间 |
| iat | Unix时间戳 | 1700796647 | RFC7519标准字段，指明令牌签发时间 |
| nbf | Unix时间戳 | 1700796647 | RFC7519标准字段，指明令牌开始生效时间 |
| iss | 字符串 | OpenAnolis-Attestation-Service | RFC7519标准字段，指明令牌签发者 |
| jti | 字符串 | BqLoaMQHql | RFC7519标准字段，在这里设置为10位随机字符，防止令牌重放攻击 |
| jwk | JSON结构 | / | JSON Web Key格式的令牌验签公钥，包含公钥对应的证书的地址，内容详见[JWK (Public Key)](#jwk-public-key) |
| policy_ids | 字符串数组 | ["default", "id_1"] | 本次验证所使用的策略集合的ID列表 |
| evaluation-reports | JSON结构数组 | / | 本次验证中每个策略所对应的验证结果报告列表，报告的内容字段详见[Evaluation Report](#evaluation-report) |
| tcb-status | JSON结构 | /| 解析并序列化后的TEE证据内容，JSON结构的内容特定于TEE类型，详见[TCB Status](./tcb_status.md) |

### JWK (Public Key)

| 名称 | 类型 | 示例值 | 描述 |
| :-----| :---- | :-----| :---- |
| kty | 字符串 | RSA | RFC7517标准字段，表征密钥类型，此处固定为RSA |
| alg | 字符串 | RS384 | RFC7517标准字段，表征密钥用途算法，此处固定为RS384 |
| n | 字符串 | / | RFC7517标准字段，RSA公钥组件值，Base64编码 |
| e | 字符串 | / | RFC7517标准字段，RSA公钥组件值，Base64编码 |
| x5u | 字符串 | https://attestation.openanolis.cn/certificate | 此公钥对应的X.509证书的下载地址 |

### Evaluation Report

| 名称 | 类型 | 示例值 | 描述 |
| :-----| :---- | :-----| :---- |
| policy-id | 字符串 | default | 生成这份评估报告时所使用的策略的ID |
| passed | 布尔值 | true | 表征用此策略验证的结果是否通过 |
| evaluation-report | 字符串 | / | OPA策略引擎的原生输出，特定于自定义的策略内容 |

## Token Example

以下是一个真实的SGX OAAS Token：

```
eyJhbGciOiJSUzM4NCIsInR5cCI6IkpXVCJ9.eyJldmFsdWF0aW9uLXJlcG9ydHMiOlt7ImV2YWx1YXRpb24tcmVwb3J0Ijoie1wiYWxsb3dcIjp0cnVlfSIsInBhc3NlZCI6dHJ1ZSwicG9saWN5LWlkIjoiZGVmYXVsdCJ9XSwiZXhwIjoxNzAwNzk2OTQ3LCJpYXQiOjE3MDA3OTY2NDcsImlzcyI6Ik9wZW5Bbm9saXMtQXR0ZXN0YXRpb24tU2VydmljZSIsImp0aSI6IkJxTG9hTVFIcWwiLCJqd2siOnsiYWxnIjoiUlMzODQiLCJlIjoiQVFBQiIsImt0eSI6IlJTQSIsIm4iOiJ3WTdxTkJCSzhhb2l3WUFjaHBsNmVYaDN0d1Rnblo2RVY1Sl9obTc3aU5YenhlbHlSVEhtc3hHMFF3bFRMWTRrbVJZcG84QnNXdUJpeFk3UlBldHBKY2pPa284T1E4cEt2bW9HSkZkMGNwSGlqbEhVMGU1NnZKMmZRemI5bUN2bDFMRFhKbFdSR0poUy1FZTNmcWNrUUZsT2dqZUQ5TS1GMXBtMW5zYk1JNFdlMW41NDBzenRUVVhUS1YyZFZUdzNIaVZfdTB0U3pLblc5THVvWWQzcVpMVjNhV0VibzZDSVRuRUVFVFFyd1VzRmU2QVNBX1EwWlBtUUdLcktGNFhTWEVSYVBPNmREOE5UZjE5T1ROdkQtUU9POWpFT0g0MmNGN3ZGTVYxaVJST0ltazU2azI5dFhUYS1NYW42NDB4ZjVIMjFYZjVQaWQtMmxBSXVxa1hOZlEiLCJ4NXUiOiJodHRwczovL2F0dGVzdGF0aW9uLm9wZW5hbm9saXMuY24vY2VydGlmaWNhdGUifSwibmJmIjoxNzAwNzk2NjQ3LCJwb2xpY3ktaWRzIjpbImRlZmF1bHQiXSwidGNiLXN0YXR1cyI6eyJzZ3guYm9keS5hdHRyaWJ1dGVzLmZsYWdzIjoiMDcwMDAwMDAwMDAwMDAwMCIsInNneC5ib2R5LmF0dHJpYnV0ZXMueGZybSI6ImU3MDAwMDAwMDAwMDAwMDAiLCJzZ3guYm9keS5jb25maWdfaWQiOiIwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMCIsInNneC5ib2R5LmNvbmZpZ19zdm4iOiIwMDAwIiwic2d4LmJvZHkuY3B1X3N2biI6IjA2MDYwYzBjZmZmZjAwMDAwMDAwMDAwMDAwMDAwMDAwIiwic2d4LmJvZHkuaXN2X2V4dF9wcm9kX2lkIjoiMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAiLCJzZ3guYm9keS5pc3ZfZmFtaWx5X2lkIjoiMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAiLCJzZ3guYm9keS5pc3ZfcHJvZF9pZCI6IjAwMDAiLCJzZ3guYm9keS5pc3Zfc3ZuIjoiMDAwMCIsInNneC5ib2R5Lm1pc2Nfc2VsZWN0IjoiMDEwMDAwMDAiLCJzZ3guYm9keS5tcl9lbmNsYXZlIjoiOGYxNzNlNDYxM2ZmMDVjNTJhYWYwNDE2MmQyMzRlZGFlOGM5OTc3ZWFlNDdlYjIyOTlhZTE2YTU1MzAxMWM2OCIsInNneC5ib2R5Lm1yX3NpZ25lciI6IjgzZDcxOWU3N2RlYWNhMTQ3MGY2YmFmNjJhNGQ3NzQzMDNjODk5ZGI2OTAyMGY5YzcwZWUxZGZjMDhjN2NlOWUiLCJzZ3guYm9keS5yZXBvcnRfZGF0YSI6Ijc0NjU3Mzc0MDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwIiwic2d4LmJvZHkucmVzZXJ2ZWQxIjoiMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwIiwic2d4LmJvZHkucmVzZXJ2ZWQyIjoiMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMCIsInNneC5ib2R5LnJlc2VydmVkMyI6IjAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAiLCJzZ3guYm9keS5yZXNlcnZlZDQiOiIwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAiLCJzZ3guaGVhZGVyLmF0dF9rZXlfZGF0YV8wIjoiMDAwMDAwMDAiLCJzZ3guaGVhZGVyLmF0dF9rZXlfdHlwZSI6IjAyMDAiLCJzZ3guaGVhZGVyLnBjZV9zdm4iOiIwZDAwIiwic2d4LmhlYWRlci5xZV9zdm4iOiIwODAwIiwic2d4LmhlYWRlci51c2VyX2RhdGEiOiJkY2NkZTliMzFjZTg4NjA1NDgxNzNiYjRhMmE1N2ExNjAwMDAwMDAwIiwic2d4LmhlYWRlci52ZW5kb3JfaWQiOiI5MzlhNzIzM2Y3OWM0Y2E5OTQwYTBkYjM5NTdmMDYwNyIsInNneC5oZWFkZXIudmVyc2lvbiI6IjAzMDAifX0.lhHsIWq6Djl5xeYKFbr3DfTM0yLydvwBsUFVHI8-C1E-3qY46rvpyQ-W1-mnaRYFRUiLBrOH0vYPN2292UhuWiYxh8lzmFEcxM7CdiDDHVdsMt0T0rn7pZk8lKQf6rhIjBtqzdiEGJerSlZqrj8fJZofF4XJb4alDLvMyNw5lgywlWUvS_QJklqa-UbK8enElty9SGRAqYGcHQUEd5iYJzctWr7JjicKodfGCh4757dhq5wB_4ZgPg8RKD3SYf9GcHCp1HtqjmVVDQDPMHBM9m1dsL24YoSvD9fdXdSICMWEr5IlWi0RuTTHflH_xSKNp2SvrkopIv0JRtGy2oPXQA
```

下面对上述Token的Header和Payload部分进行Base64解码，得到的结果如下：

### Header

```
{
    "alg":"RS384",
    "typ":"JWT"
}
```

### Payload

```
{
    "exp":1700796947,
    "iat":1700796647,
    "iss":"OpenAnolis-Attestation-Service",
    "jti":"BqLoaMQHql",
    "jwk":{
        "kty":"RSA",
        "alg":"RS384",
        "e":"AQAB",
        "n":"wY7qNBBK8aoiwYAchpl6eXh3twTgnZ6EV5J_hm77iNXzxelyRTHmsxG0QwlTLY4kmRYpo8BsWuBixY7RPetpJcjOko8OQ8pKvmoGJFd0cpHijlHU0e56vJ2fQzb9mCvl1LDXJlWRGJhS-Ee3fqckQFlOgjeD9M-F1pm1nsbMI4We1n540sztTUXTKV2dVTw3HiV_u0tSzKnW9LuoYd3qZLV3aWEbo6CITnEEETQrwUsFe6ASA_Q0ZPmQGKrKF4XSXERaPO6dD8NTf19OTNvD-QOO9jEOH42cF7vFMV1iRROImk56k29tXTa-Man640xf5H21Xf5Pid-2lAIuqkXNfQ",
        "x5u":"https://attestation.openanolis.cn/certificate"
    },
    "nbf":1700796647,
    "policy-ids":[
        "default"
    ],
    "evaluation-reports":[
        {
            "policy-id":"default",
            "passed":true,
            "evaluation-report":"{\"allow\":true}"
        }
    ],
    "tcb-status":{
        "sgx.body.attributes.flags":"0700000000000000",
        "sgx.body.attributes.xfrm":"e700000000000000",
        "sgx.body.config_id":"00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
        "sgx.body.config_svn":"0000",
        "sgx.body.cpu_svn":"06060c0cffff00000000000000000000",
        "sgx.body.isv_ext_prod_id":"00000000000000000000000000000000",
        "sgx.body.isv_family_id":"00000000000000000000000000000000",
        "sgx.body.isv_prod_id":"0000",
        "sgx.body.isv_svn":"0000",
        "sgx.body.misc_select":"01000000",
        "sgx.body.mr_enclave":"8f173e4613ff05c52aaf04162d234edae8c9977eae47eb2299ae16a553011c68",
        "sgx.body.mr_signer":"83d719e77deaca1470f6baf62a4d774303c899db69020f9c70ee1dfc08c7ce9e",
        "sgx.body.report_data":"74657374000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
        "sgx.body.reserved1":"000000000000000000000000",
        "sgx.body.reserved2":"0000000000000000000000000000000000000000000000000000000000000000",
        "sgx.body.reserved3":"0000000000000000000000000000000000000000000000000000000000000000",
        "sgx.body.reserved4":"000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
        "sgx.header.att_key_data_0":"00000000",
        "sgx.header.att_key_type":"0200",
        "sgx.header.pce_svn":"0d00",
        "sgx.header.qe_svn":"0800",
        "sgx.header.user_data":"dccde9b31ce8860548173bb4a2a57a1600000000",
        "sgx.header.vendor_id":"939a7233f79c4ca9940a0db3957f0607",
        "sgx.header.version":"0300"
    }
}
```