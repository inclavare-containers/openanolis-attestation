# Evidence Collector

## Usage

安装rust编译工具链：
```
curl https://sh.rustup.rs -sSf | bash -s -- -y;
```

指定需要支持的TEE类型编译 (当前支持的TEE类型有：csv、tdx、sgx、snp、cca)，例如需要支持CSV、SNP和TDX三种TEE：

```
cargo build --no-default-feature --features csv,snp,tdx
```

运行工具收集TEE平台证据（请注意需要在真实的TEE环境内执行，且需要保证该TEE环境开启了证明支持）

```
./evidence-collector
```

上述命令会自动收集当前运行环境TEE平台的证据，并组织为[OAAS API支持的Evidence格式](../../docs/evidence.md)，Base64编码后写入当前目录下的`evidence.txt`文件中。

另外可以通过参数`--report-data`设置需要包含在证据中的Report Data（Base64编码），注意不能超过当前TEE支持的report data位数上限：

```
./evidence-collector --report-data dGVzdDEyMzQ1Njc4OTAK
```