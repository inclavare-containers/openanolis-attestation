# Evidence

本文介绍当前API支持的TEE类型对应的Evidence格式.

## Intel TDX/SGX

```
{
    "quote": <Base64 Encoded Quote Binary>
}
```

## AMD SEV-SNP

```
{
    "attestation_report": <SEV AttestationReport Structure>,
    "cert_chain": [
        <SEV CertTableEntry Structure>，
        <SEV CertTableEntry Structure>，
        ...
    ]
}
```
结构体字段和内容类型参考：

- [SEV AttestationReport Structure](https://docs.rs/sev/latest/sev/firmware/guest/struct.AttestationReport.html)
- [SEV CertTableEntry Structure](https://docs.rs/sev/latest/sev/firmware/host/struct.CertTableEntry.html)

## HYGON CSV

```
{
    "attestation_report": <CSV AttestationReport Structure>,
    "cert_chain": {
        "hsk": <CSV CA Certificate Structure>,
        "cek": <CSV Certificate Structure>,
        "pek": <CSV Certificate Structure>,
    }
}
```

结构体字段和内容类型参考：

- [CSV AttestationReport Structure](https://gitee.com/anolis/csv-rs/blob/master/src/api/guest/types.rs#L135)
- [CSV CA Certificate Structure](https://gitee.com/anolis/csv-rs/blob/master/src/api/guest/types.rs#L135)
- [CSV Certificate Structure](https://gitee.com/anolis/csv-rs/blob/master/src/certs/csv/cert/mod.rs#L55)

## Arm CCA

```
{
    "token": <u8 array CCA token>
}
```

## Sample (FOR TEST ONLY)

Sample并不是一个真实的TEE类型，它仅可用于测试OAAS实例的连通性。

```
{
    "svn": "1",
    "report_data": <string>,
    "init_data_hash": <string>,
}
```