# TCB Status

本文介绍编写策略时所需要了解的OAAS Token中包含的不同TEE类型的TCB Status字段名称和类型。

## Intel TDX

| 名称 | 类型 |
| :-----| :---- |
| tdx.quote.header.version | 16进制小写HEX编码字符串 |
| tdx.quote.header.att_key_type | 16进制小写HEX编码字符串 |
| tdx.quote.header.tee_type | 16进制小写HEX编码字符串 |
| tdx.quote.header.reserved | 16进制小写HEX编码字符串 |
| tdx.quote.header.vendor_id | 16进制小写HEX编码字符串 |
| tdx.quote.header.user_data | 16进制小写HEX编码字符串 |
| tdx.quote.body.mr_config_id | 16进制小写HEX编码字符串 |
| tdx.quote.body.mr_owner | 16进制小写HEX编码字符串 |
| tdx.quote.body.mr_owner_config | 16进制小写HEX编码字符串 |
| tdx.quote.body.mr_td | 16进制小写HEX编码字符串 |
| tdx.quote.body.mrsigner_seam | 16进制小写HEX编码字符串 |
| tdx.quote.body.report_data | 16进制小写HEX编码字符串 |
| tdx.quote.body.seam_attributes | 16进制小写HEX编码字符串 |
| tdx.quote.body.td_attributes | 16进制小写HEX编码字符串 |
| tdx.quote.body.mr_seam | 16进制小写HEX编码字符串 |
| tdx.quote.body.tcb_svn | 16进制小写HEX编码字符串 |
| tdx.quote.body.xfam | 16进制小写HEX编码字符串 |

## Intel SGX

| 名称 | 类型 |
| :-----| :---- |
| sgx.header.version | 16进制小写HEX编码字符串 |
| sgx.header.vendor_id | 16进制小写HEX编码字符串 |
| sgx.header.user_data | 16进制小写HEX编码字符串 |
| sgx.header.qe_svn | 16进制小写HEX编码字符串 |
| sgx.header.pce_svn | 16进制小写HEX编码字符串 |
| sgx.header.att_key_type | 16进制小写HEX编码字符串 |
| sgx.header.att_key_data_0 | 16进制小写HEX编码字符串 |
| sgx.body.reserved1 | 16进制小写HEX编码字符串 |
| sgx.body.reserved2 | 16进制小写HEX编码字符串 |
| sgx.body.reserved3 | 16进制小写HEX编码字符串 |
| sgx.body.reserved4 | 16进制小写HEX编码字符串 |
| sgx.body.report_data | 16进制小写HEX编码字符串 |
| sgx.body.mr_signer | 16进制小写HEX编码字符串 |
| sgx.body.mr_enclave | 16进制小写HEX编码字符串 |
| sgx.body.misc_select | 16进制小写HEX编码字符串 |
| sgx.body.isv_svn | 16进制小写HEX编码字符串 |
| sgx.body.isv_prod_id | 16进制小写HEX编码字符串 |
| sgx.body.isv_family_id | 16进制小写HEX编码字符串 |
| sgx.body.isv_ext_prod_id | 16进制小写HEX编码字符串 |
| sgx.body.cpu_svn | 16进制小写HEX编码字符串 |
| sgx.body.config_svn | 16进制小写HEX编码字符串 |
| sgx.body.config_id | 16进制小写HEX编码字符串 |
| sgx.body.attributes.xfrm | 16进制小写HEX编码字符串 |
| sgx.body.attributes.flags | 16进制小写HEX编码字符串 |


## AMD SEV-SNP

| 名称 | 类型 |
| :-----| :---- |
| snp.policy_abi_major | u64字符串 |
| snp.policy_abi_major | u64字符串 |
| snp.policy_smt_allowed | u64字符串 |
| snp.policy_migrate_ma | u64字符串 |
| snp.policy_debug_allowed | u64字符串 |
| snp.policy_single_socket | u64字符串 |
| snp.reported_tcb_bootloader | u8字符串 |
| snp.reported_tcb_tee | u8字符串 |
| snp.reported_tcb_snp | u8字符串 |
| snp.reported_tcb_microcode | u8字符串 |
| snp.platform_tsme_enabled | u64字符串 |
| snp.platform_smt_enabled | u64字符串 |
| snp.measurement | Base64编码（48字节度量值） |


## HYGON CSV

| 名称 | 类型 |
| :-----| :---- |
| csv.policy_nodbg | 布尔值字符串 (0 or 1) |
| csv.policy_noks | 布尔值字符串 (0 or 1) |
| csv.policy_es | 布尔值字符串 (0 or 1) |
| csv.policy_nosend | 布尔值字符串 (0 or 1) |
| csv.policy_domain | 布尔值字符串 (0 or 1) |
| csv.policy_csv | 布尔值字符串 (0 or 1) |
| csv.policy_csv3 | 布尔值字符串 (0 or 1) |
| csv.policy_asid_reuse | 布尔值字符串 (0 or 1) |
| csv.policy_hsk_version | 字符串 |
| csv.policy_cek_version | 字符串 |
| csv.policy_api_major | 字符串 |
| csv.policy_api_minor | 字符串 |
| csv.vm_id | Base64编码 |
| csv.vm_version | Base64编码 |
| csv.measurement | Base64编码 |
| csv.report_data | Base64编码 |

## Arm CCA

| 名称 | 类型 |
| :-----| :---- |
| cca.cca-realm-initial-measurement | 字符串 |