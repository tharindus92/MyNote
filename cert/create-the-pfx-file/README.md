# Create the PFX File



### Create the PFX File

The rui.pfx file is a concatenation of the system’s certificate and private key, exported in the PFX format. The file is copied to the subdirectory on the vCenter Server system.

Personal Information Exchange Format \(PFX\) enables transfer of certificates and their private keys from one computer to another or to removable media. The Microsoft Windows CryptoAPI uses the PFX format, also known as PKCS \#12.

#### Procedure

Export the certificate and key file together to PFX format using OpenSSL.

```text
openssl pkcs12 -export -in rui.crt -inkey rui.key -name rui -passout pass:testpassword -out rui.pfx
```

You must use the password testpassword.



In Windows platform, these file types are used for certificate information. Normally used for SSL certificate and Public Key Infrastructure \(X.509\).

* **CER files:** CER file is used to store X.509 certificate. Normally used for SSL certification to verify and identify web servers security. The file contains information about certificate owner and public key. A CER file can be in binary \(ASN.1 DER\) or encoded with Base-64 with header and footer included \(PEM\), Windows will recognize either of these layout.
* **PVK files:** Stands for Private Key. Windows uses PVK files to store private keys for code signing in various Microsoft products. PVK is proprietary format.
* **PFX files** Personal Exchange Format, is a PKCS12 file. This contains a variety of cryptographic information, such as certificates, root authority certificates, certificate chains and private keys. It’s cryptographically protected with passwords to keep private keys private and preserve the integrity of the root certificates. The PFX file is also used in various Microsoft products, such as IIS.



## DigiCert Certificate Utility SSL Certificate Export Instructions \(PFX Format\)

### Exporting Your SSL Certificate from a Microsoft Server for Importing to Another Microsoft Server

**Background**

Windows servers use .pfx files that contain the public key file \(SSL certificate file\) and the associated private key file. DigiCert provides your SSL certificate file \(public key file\). You use your server to generate the associated private key file as part of the CSR.

You need both the public and private keys for an SSL certificate to function. So, if you need to transfer your SSL certificates from one server to another, you need to export is as a .pfx file.

**Export Prerequisite**

To create a .pfx file, the SSL certificate and its corresponding private key must be on the same computer/workstation. You may need to import the certificate to the computer that has the associated private key stored on it. \(e.g., the laptop/desktop computer where you created the CSR\) before you can successfully export it as a .pfx file.

For help importing the certificate, see [SSL Certificate Importing Instructions: DigiCert Certificate Utility](https://www.digicert.com/util/ssl-certificate-installation-using-digicert-utility-for-microsoft-servers.htm).

#### **How to Export Your SSL Certificate w/Private Key Using the DigiCert Certificate Utility**

These instructions explain how to export an installed SSL certificate from a Microsoft server and its corresponding private key as a .pfx file for importing to another server. If you need your SSL Certificate in Apache .key format, please see [Export a Windows SSL Certificate to an Apache Server \(PEM Format\)](https://www.digicert.com/util/copy-ssl-from-windows-iis-to-apache-using-digicert-certificate-utility.htm).

1. On your Windows Server, download and save the [DigiCert® Certificate Utility for Windows](https://www.digicert.com/util/DigiCertUtil.exe) executable \(**DigiCertUtil.exe**\).
2. Run the DigiCert® Certificate Utility for Windows \(double-click **DigiCertUtil**\).
3. In the **DigiCert Certificate Utility for Windows©**, click **SSL** \(gold lock\), select the certificate that you want to export as a .pfx file, and then click **Export Certificate**.

   ![Select Certificate then click Export Button](https://www.digicert.com/images/DigiCertUtil/utility-pfx-export-1.png)

4. In the **Certificate Export** wizard, select **Yes, export the private key**, select **pfx file**, and then check **Include all certificates in the certification path if possible**, and finally, click **Next**.

   A .pfx file uses the same format as a .p12 or PKCS12 file.

   **Note:** If the **Yes, export the private key** option is grayed out \(not unusable\), the certificate's matching private key is not on that computer. This prevents you from being able to create the .pfx certificate file. To fix this problem, you will need to import the certificate to the same machine where the certificate's CSR was created. See [Export Prerequisite](https://www.digicert.com/util/pfx-certificate-management-utility-import-export-instructions.htm#export_prerequisites).

   ![Select Certificate then click Export Button](https://www.digicert.com/images/DigiCertUtil/utility-pfx-export-2.png)

5. In the **Password** and **Confirm Password** boxes, enter and confirm your password, and then, click **Next**.

   **Note:**    This password is used when you import this SSL certificate onto other Windows type servers or other servers or devices that accept a .pfx file.

   ![Select Certificate then click Export Button](https://www.digicert.com/images/DigiCertUtil/utility-pfx-export-3.png)

6. In the **File name** box, click **…** to browse for and select the location and file name where you want to save the .pfx file, provide a file name \(i.e. _mySSLCertificate_\), click **Save**, and then, click **Finish**.

   ![Select Certificate then click Export Button](https://www.digicert.com/images/DigiCertUtil/utility-pfx-export-4.png)

7. After you receive the _"Your certificate and key have been successfully exported"_ message, click **OK**.

   ![Select Certificate then click Export Button](https://www.digicert.com/images/DigiCertUtil/utility-pfx-export-6.png)

### Import PFX Certificate into Microsoft Windows Server and Configure it

To import your certificate to your server using the DigiCert Certificate Utility, you need to follow the instructions for that particular server type:

| [IIS 10](https://www.digicert.com/util/copy-ssl-certificate-from-any-microsoft-server-to-iis10-server.htm#import-certificate) | [Exchange 2013](https://www.digicert.com/util/copy-an-ssl-certificate-to-exchange-2013-server.htm) |
| :--- | :--- |
| [IIS 8](https://www.digicert.com/util/copy-an-ssl-certificate-from-one-windows-iis8-server-to-another-iis8-server.htm) | [Exchange 2010](https://www.digicert.com/util/copy-an-ssl-certificate-from-one-exchange-2010-server-to-another-exchange-2010-server.htm) |
| [IIS 7](https://www.digicert.com/util/copy-an-ssl-certificate-from-one-windows-iis-7-server-to-another-iis-7-server.htm) | [Exchange 2007](https://www.digicert.com/util/copy-an-ssl-certificate-from-one-exchange-2007-server-to-another-exchange-2007-server.htm) |
| [IIS 6](https://www.digicert.com/util/copy-an-ssl-certificate-from-one-windows-iis-6-server-to-another-iis-6-server.htm) |  |
|  |  |

