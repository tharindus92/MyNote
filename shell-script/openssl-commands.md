# OpenSSL Commands



### General OpenSSL Commands

These commands allow you to generate CSRs, Certificates, Private Keys and do other miscellaneous tasks.

* **Generate a new private key and Certificate Signing Request**

  ```text
  openssl req -out CSR.csr -new -newkey rsa:2048 -nodes -keyout privateKey.key
  ```

* **Generate a self-signed certificate \(see** [**How to Create and Install an Apache Self Signed Certificate**](https://www.sslshopper.com/article-how-to-create-and-install-an-apache-self-signed-certificate.html) **for more info\)**

  ```text
  openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out certificate.crt
  ```

* **Generate a certificate signing request \(CSR\) for an existing private key**

  ```text
  openssl req -out CSR.csr -key privateKey.key -new
  ```

* **Generate a certificate signing request based on an existing certificate**

  ```text
  openssl x509 -x509toreq -in certificate.crt -out CSR.csr -signkey privateKey.key
  ```

* **Remove a passphrase from a private key**

  ```text
  openssl rsa -in privateKey.pem -out newPrivateKey.pem
  ```

### Checking Using OpenSSL

If you need to check the information within a Certificate, CSR or Private Key, use these commands. You can also [check CSRs](https://www.sslshopper.com/csr-decoder.html) and [check certificates](https://www.sslshopper.com/certificate-decoder.html) using our online tools.

* **Check a Certificate Signing Request \(CSR\)**

  ```text
  openssl req -text -noout -verify -in CSR.csr
  ```

* **Check a private key**

  ```text
  openssl rsa -in privateKey.key -check
  ```

* **Check a certificate**

  ```text
  openssl x509 -in certificate.crt -text -noout
  ```

* **Check a PKCS\#12 file \(.pfx or .p12\)**

  ```text
  openssl pkcs12 -info -in keyStore.p12
  ```

### Debugging Using OpenSSL

If you are receiving an error that the private doesn't match the certificate or that a certificate that you installed to a site is not trusted, try one of these commands. If you are trying to verify that an SSL certificate is installed correctly, be sure to check out the [SSL Checker](https://www.sslshopper.com/ssl-checker.html).

* **Check an MD5 hash of the public key to ensure that it matches with what is in a CSR or private key**

  ```text
  openssl x509 -noout -modulus -in certificate.crt | openssl md5
  openssl rsa -noout -modulus -in privateKey.key | openssl md5
  openssl req -noout -modulus -in CSR.csr | openssl md5
  ```

* **Check an SSL connection. All the certificates \(including Intermediates\) should be displayed**

  ```text
  openssl s_client -connect www.paypal.com:443
  ```

### Converting Using OpenSSL

These commands allow you to convert certificates and keys to different formats to make them compatible with specific types of servers or software. For example, you can convert a normal PEM file that would work with Apache to a PFX \(PKCS\#12\) file and use it with Tomcat or IIS. Use our [SSL Converter to convert certificates](https://www.sslshopper.com/ssl-converter.html) without messing with OpenSSL.

* **Convert a DER file \(.crt .cer .der\) to PEM**

  ```text
  openssl x509 -inform der -in certificate.cer -out certificate.pem
  ```

* **Convert a PEM file to DER**

  ```text
  openssl x509 -outform der -in certificate.pem -out certificate.der
  ```

* **Convert a PKCS\#12 file \(.pfx .p12\) containing a private key and certificates to PEM**

  ```text
  openssl pkcs12 -in keyStore.pfx -out keyStore.pem -nodes
  ```

  You can add -nocerts to only output the private key or add -nokeys to only output the certificates.

* **Convert a PEM certificate file and a private key to PKCS\#12 \(.pfx .p12\)**

  ```text
  openssl pkcs12 -export -out certificate.pfx -inkey privateKey.key -in certificate.crt -certfile CACert.crt
  ```

