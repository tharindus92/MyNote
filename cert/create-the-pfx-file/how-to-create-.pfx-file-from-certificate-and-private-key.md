# How to create .pfx file from certificate and private key?



Before you can order an SSL certificate, it is recommended that you generate a Certificate Signing Request \(CSR\) from your server or device,

```text
openssl pkcs12 -export -out domain.name.pfx -inkey domain.name.key -in domain.name.crt
```



