# Certificate Object( 证书对象)

* `data` String  -  PEM编码数据
* `issuer` [CertificatePrincipal](certificate-principal.md) - 发行人主体
* `issuerName` String  - 发行商的公用名
* `issuerCert`证书 - 颁发者证书(如果不是自签名)
* `subject` [CertificatePrincipal](certificate-principal.md)  - 主题主体
* `subjectName` String  - 主题公共名
* `serialNumber` String  - 十六进制值表示字符串
* `validStart` Number  - 证书的有效起始日期(以秒为单位)
* `validExpiry` Number  - 证书的有效结束日期(以秒为单位)
* `fingerprint` String  - 证书的指纹