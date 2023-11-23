# OAAS Policy

本文介绍基本的OAAS Attestation Policy编写与设置。

## Introduction

OAAS的策略引擎由CNCF毕业开源项目[Open Policy Agent (OPA)](https://www.openpolicyagent.org/)实现，策略采用Rego语法编写。

策略引擎的输入为[TCB Status](./tcb_status.md), 用户可以通过合理地编写策略，对TCB Status中的字段进行细粒度的筛查过滤或逻辑判断。策略的主要目的是在基本的硬件证据验证之后增加特定于用户自身的自定义验证能力。

OPA是一种强大且灵活的策略引擎实现，通过对Rego语法的简单的学习，用户可以编写任意他们想要的复杂判断逻辑，并且生成任意想要的输出结构（输出会被包含在OAAS Token中）。

## Example

为了方便OAAS的用户在没有学习过OPA的Rego策略语法的前提下，能够快速上手设置简单的自定义策略来控制策略引擎的验证，下面提供一个通用的OAAS策略示例，用户可以将此示例作为模版来生成自己想要的简单策略。

### Goals

假设某个OAAS实例的用户对远程证明验证的自定义需求如下：

1. 要求Intel TDX平台的固件度量值为“abc123”、“xyz456”中的一个，否则认为平台不可信。
2. 要求Intel TDX平台的版本为“1“，否则认为平台不可信。
3. 要求HYGON CSV平台的度量值为“12345678”，否则认为平台不可信。
4. 要求AMD SEV-SNP平台的TCB微码为“6“、“3”、“1”中的一个，否则认为平台不可信。

### Policy Content

针对上述验证需求，该用户可以编写如下策略并设置到自己的OAAS实例中，即可实现上述目标：

```
package policy

import future.keywords.if

default allow = false

# The allowed reference values for the specific field in the quote
reference_tdx_mr_td = [ "abc123", "xyz456" ]
reference_tdx_version = [ "1" ]
reference_csv_measurement = [ "12345678" ]
reference_snp_tcb_microcode = [ "6", "3", "1" ]

# Determine TEE type from input TCB status
tee_type(tee) {
	some field
    input[field]
    startswith(field, tee)
}

allow if {
    tee_type("tdx")
    input["tdx.quote.body.mr_td"] == reference_tdx_mr_td[_]
    input["tdx.quote.header.version"] == reference_tdx_version[_]
}
allow if {
    tee_type("csv")
    input["csv.measurement"] == reference_csv_measurement[_]
}
allow if {
    tee_type("snp")
    input["snp.reported_tcb_microcode"] == reference_snp_tcb_microcode[_]
}
```

用户可以根据自己想要验证的[TCB Status](./tcb_status.md)中的字段，修改上述策略以满足自己的自定义需求。若上述策略模版无法满足需求，用户可以学习[OPA rego策略语法和模块](https://www.openpolicyagent.org/docs/latest/)，以编写出更多功能更强大的验证和判断逻辑。

### Set Policy

首先使用如下命令对策略文件内容进行Base64编码：

```
cat /path/to/policy.rego | base64
```

然后访问OAAS实例的[SetAttestPolicy API](./api#setattestpolicy)进行策略设置.







