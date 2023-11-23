# OAAS APIs

本文介绍OAAS实例提供的相关API接口及其各API接口的用法

- TEE远程证明

| API | 描述 |
| :-----| :---- |
| [Attestation](#attestation) | OAAS实例的核心API，用于验证TEE证据 |

- 验证策略管理

| API | 描述 |
| :-----| :---- |
| [GetAttestPolicyList](#getattestpolicylist) | 获得当前已经设置的所有策略ID与策略内容的列表 |
| [SetAttestPolicy](#setattestpolicy) | 设置新的Attestation Policy |
| [DeleteAttestPolicy](#deleteattestpolicy) | 删除指定的Attestation Policy |

## Attestation

OAAS实例的核心API，用于验证TEE证据

### 请求语法

```
POST /<USER_ID>/attestation HTTP/1.1
Host: attestation.openanolis.cn
Content-Type: application/json
Authorization: Basic <CREDENTIAL>
```

### 请求头

此接口仅涉及公共请求头，但是请注意您需要在`Authorization: Basic`中设置OAAS实例的访问凭证。

### 请求参数

请求参数以JSON格式组织在请求体中，参数如下：

| 名称 | 类型 | 是否必选 | 示例值 | 描述 |
| :-----| :---- | :-----| :---- | :---- |
| tee | 字符串 | 是 | 可以是如下值中的一个：`tdx`, `sgx`, `csv`, `snp`, `cca` | 指定了本次Attestation的TEE类型，必须是当前OAAS已经支持的TEE类型中的一种（见示例值） |
| evidence | 字符串 | 是 | / | Base64编码的TEE平台证据，其内部格式特定于TEE类型。每种TEE类型对应的证据格式详见[evidence.md](./evidence.md) |
| runtime_data | 字符串数组 | 否 | / | 可选参数，数组项内容为Base64编码的自定义数据。若提供此参数，则此参数中所有数据的`SHA384`摘要必须作为TEE证据的report data字段镶嵌，否则会验证失败 |
| policy_id | 字符串数组 | 否 | ["policy_1", "policy_2"] | 可选参数，用以指定本次验证所使用的策略集合，参数中所指定的每个策略的验证结果都会包含在响应返回值令牌中。若不设置此参数，则OAAS会使用默认策略 |

### 响应元素

响应返回值为OAAS Token, 这是一个分段Base64编码的JWT格式令牌，令牌的内容说明详见[oaas_token.md](./oaas_token.md).

### 请求示例

```
POST /3922******228/attestation HTTP/1.1
Host: attestation.openanolis.cn
Content-Type: application/json
Authorization: Basic MzkyMjQ2NDQwNzUzN******UHRsdlVNb1B6R2NW

{
    "tee": "sgx",
    "evidence": "eyJxdW90ZSI6IkF3QUB*****FvPSJ9Cg==",
}
```

为方便本文档阅读，示例中省略了`evidence`参数的部分内容（用`*`替代）。

### 响应示例

```
HTTP/1.1 200 OK
Server: nginx/1.14.1
Date: Thu, 23 Nov 2023 06:04:58 GMT
Content-Length: 3287
Connection: keep-alive

eyJhbGciOiJSUzM4NCIsInR5cCI6IkpXVCJ9.eyJldmFsdWF0aW9uLXJlcG9ydHMiOlt7ImV2YWx1YXRpb24t******IudmVyc2lvbiI6IjAzMDAifX0.qAr5l1qCpYK-02M4MDxVo_He5BBZHE8afk-dlfXwH-ODmtzEnkDlNjrExHXF7geR6w0LIEex6fjWfVeQGuq_wq6f5MuagqDIYSvEJzRfngVswE_p8RUTXhACy91-Zqd
```

为方便本文档阅读，示例中省略了token的部分内容（用`*`替代）。


## GetAttestPolicyList

获得当前已经设置的所有策略ID与内容的列表。

### 请求语法

```
GET /<USER_ID>/policy HTTP/1.1
Host: attestation.openanolis.cn
Authorization: Basic <CREDENTIAL>
```

### 请求头

此接口仅涉及公共请求头，但是请注意您需要在`Authorization: Basic`中设置OAAS实例的访问凭证。

### 请求参数

暂不支持在此API中设置请求参数。

### 响应元素

响应元素以JSON格式组织，内容元素如下：

| 名称 | 类型 | 示例值 | 描述 |
| :-----| :---- | :-----| :---- |
| policys | JSON字符串数组 | / | 由多个JSON结构体组成的数组 |

`policys`数组中每个JSON结构体的元素如下：

| 名称 | 类型 | 示例值 | 描述 |
| :-----| :---- | :-----| :---- |
| id | 字符串 | policy_id_1 | 策略ID，在策略设置时由用户指定 |
| digest | 字符串 | rBprQN9JGlBu1l+0VkxO6sgO9DSz5hAj4m8DnpBoitQ/fic1PLz4kVcALh5Csxwf | 策略内容的`SHA384`摘要（Base64编码）|
| content | 字符串 | package policy\ndefault allow = true | 策略内容，换行符用`\n`表示 |

### 请求示例

```
GET /3922******228/policy HTTP/1.1
Host: attestation.openanolis.cn
Authorization: Basic MzkyMjQ2NDQwNzUzN******UHRsdlVNb1B6R2NW
```

### 响应示例

```
HTTP/1.1 200 OK
Server: nginx/1.14.1
Date: Thu, 23 Nov 2023 06:52:40 GMT
Content-Type: application/json
Content-Length: 155
Connection: keep-alive

{
    "policys":[
        {
            "content":"package policy\ndefault allow = true",
            "digest":"rBprQN9JGlBu1l+0VkxO6sgO9DSz5hAj4m8DnpBoitQ/fic1PLz4kVcALh5Csxwf",
            "id":"default"
        }
    ]
}
```

## SetAttestPolicy

设置新的Attestation Policy

### 请求语法

```
POST /<USER_ID>/policy HTTP/1.1
Host: attestation.openanolis.cn
Authorization: Basic <CREDENTIAL>
```

### 请求头

此接口仅涉及公共请求头，但是请注意您需要在`Authorization: Basic`中设置OAAS实例的访问凭证。

### 请求参数

请求参数以JSON格式组织在请求体中，参数如下：

| 名称 | 类型 | 是否必选 | 示例值 | 描述 |
| :-----| :---- | :-----| :---- | :---- |
| type | 字符串 | 是 | rego | 当前仅支持OPA策略类型: "rego" |
| policy_id | 字符串 | 是 | policy_id_1 | 所设置的策略ID |
| policy | 字符串 | 是| / | Base64编码的策略内容 |

### 响应元素

此API无特殊响应内容

### 请求示例

```
POST /3922******228/policy HTTP/1.1
Host: attestation.openanolis.cn
Authorization: Basic MzkyMjQ2NDQwNzUzN******UHRsdlVNb1B6R2NW

{
    "type": "rego",
    "policy_id": "id_1",
    "policy": "cGFja2FnZSBwb2xpY3luZGVmYXVsdCBhbGxvdyA9IHRydWUK"
}
```

### 响应示例

```
HTTP/1.1 200 OK
Server: nginx/1.14.1
Date: Thu, 23 Nov 2023 06:52:40 GMT
Connection: keep-alive
```

## DeleteAttestPolicy

删除指定的Attestation Policy

### 请求语法

```
DELETE /<USER_ID>/policy HTTP/1.1
Host: attestation.openanolis.cn
Authorization: Basic <CREDENTIAL>
```

### 请求头

此接口仅涉及公共请求头，但是请注意您需要在`Authorization: Basic`中设置OAAS实例的访问凭证。

### 请求参数

请求参数以JSON格式组织在请求体中，参数如下：

| 名称 | 类型 | 是否必选 | 示例值 | 描述 |
| :-----| :---- | :-----| :---- | :---- |
| policy_ids | 字符串数组 | 是 | ["id_1", "id_2"] | 需要删除的policy ID列表 |

### 响应元素

此API无特殊响应内容

### 请求示例

```
DELETE /3922******228/policy HTTP/1.1
Host: attestation.openanolis.cn
Authorization: Basic MzkyMjQ2NDQwNzUzN******UHRsdlVNb1B6R2NW

{
    "policy_ids": [
        "id_1",
        "id_2"
    ]
}
```

### 响应示例

```
HTTP/1.1 200 OK
Server: nginx/1.14.1
Date: Thu, 23 Nov 2023 06:52:40 GMT
Connection: keep-alive
```


