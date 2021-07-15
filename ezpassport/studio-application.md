# STUDIO APPLICATION

## 1. Create Google bucket

### 1.1 **Navigate to Storage —&gt; Browser from the left navigation pane.**

![Image](https://lh5.googleusercontent.com/49bOWWXLiFe20neCUrV3VvngrF2sMi975kVNCTbiHOm_Th797QOyoLQKV1HQzj99CDgiwhLkxYsd9fI_yAvI1cjj-DNlUvmLsNE-7It0oglhGts-rUNxD21LAdm1iPz_tkb5mUY)

### 1.2  **Select Create Bucket.**

![](../.gitbook/assets/image%20%2816%29.png)

### 1.3  **Provide a unique and meaningful name for the bucket name and then, click Continue.**

![Image](https://lh3.googleusercontent.com/QZl5Se2lyDXr6z62gwYcCi4elcehKLJwOXuj_DzdWeubfAibPyZGz-357b8N8Gxxhk-mFv5ktyyEGHYByXZnnL5GgJ9d4ugp8IyYRmGNVboo1vhMKrfafrydwvv6FfKXR9xrCmc)

### **1.4 Select the location type and click Continue.**

![Image](https://lh6.googleusercontent.com/50Pk4kgz-qowEPewZVoKU9zSWqQBeDG_kTTM3zVYNtc9DG3UQWzXD6iGWWez9JcKum5bC4lo8So92df8VjTut0s8V2slVQ73uHZUeHteMkvCilApuihpkQMyK8YH_pFz2lMzI00)

### **1.5 Choose the Storage Class and click Continue.**

![Image](https://lh4.googleusercontent.com/tIoTR0AW4bpuO_nwd9Dc8_vGMZQYPW649CFHz-NPrL_XH1wEwwII3YBKeWw2cnWyW6WoN3OrSusheQOSwdkIK3URQycRFo7fKI7CW0zgeLhS3PMAEN6XBMm1RsnqzrT4JsKLnig)

### **1.6 Select Fine-grained as the access control type and click Continue.**

![](../.gitbook/assets/image%20%2821%29.png)

### **1.7 Select the needed encryption type and click CREATE**

![Image](https://lh5.googleusercontent.com/fPsyeUQML4VZyVkoyC_mSTYuJlBpjs4fQuAdH5Qa0Ok6q6X9lkH74hcLVAAyfDwNQAZ2R0y_cIitOhLP2LS1uqJr4feQEDOGX1o1oVXNKtEBr4_NQYhgXImO1xlRZLC2Trhieyw)

### **1.8  If the bucket creation is successful, you should get the below.**

![Image](https://lh5.googleusercontent.com/xT21Sp2RWOzfVQuchYnuUdn_E0ALctVLMFPIqe1Gb8HOO-RddD4eija5Rl6EWSJuFlLIRfiQS43N8UfcWp_CH3YnSMdy5IMGbhq3dum72uRumbeXBzec_9dG93ejCR115BU8IMY)

### **1.9  In order to provide bucket access, navigate to Permissions.**

![Image](https://lh4.googleusercontent.com/9MfCmqgaHoclWrWVyTO-r42bKrtz8yxthzgENS8IeKMCcJMNmMl9gqIl8PDQcr-RJss_28oGnYVIr3Y88du3X9zVeXfv7iA_ebU27oflqWnWd5Pp4p72OivY6KrEO-D9BYyQCF0)

### **1.10 Provide Storage Object Admin role to public.**

![](../.gitbook/assets/image%20%2819%29.png)

## **2. Updates.json push in to google bucket.**

```javascript
{
    "Version": "0.1.14-20210629.090457-1",
    "RunCommand": "EZPassport.Studio.Main.exe",
    "AppParams": "",
    "WSB": "netcoreapp3.0",
    "Files": [
        {
            "Name": "ezp-1.0.zip",
            "URL": "https://storage.googleapis.com/ezp-artifacts/ezp-1.0.zip"
        },
        {
            "Name": "lib-1.0.zip",
            "URL": "https://storage.googleapis.com/ezp-artifacts/lib-1.0.zip"
        },
        {
            "Name": "python37-1.0.zip",
            "URL": "https://storage.googleapis.com/ezp-artifacts/python37-1.0.zip"
        },
        {
            "Name": "studio-application-0.1.14-20210629.090457-1.zip",
            "URL": "https://storage.googleapis.com/ezp-artifacts/studio-application-0.1.14-20210629.090457-1.zip"
        }
    ],
    "Envs": {
        "ML_PROC_EXEC_PATH": "python37/python.exe",
        "ML_PROC_ARGS": "-m ezpassport.server",
        "ML_PROC_WD": "../"
    }
}
```

## **3. Studio Application push into google bucket**

Get code from nexus and push it into google bucket.

{% embed url="https://nexus.zone24x7.lk/\#browse/browse:releases:com%2Fzone%2Fppema%2Fstudio-application" %}

![](../.gitbook/assets/capture.png)

## 4. python37 package **push into google bucket.**

Zip python37 folder and push into google bucket.

![](../.gitbook/assets/image%20%2822%29.png)

{% embed url="https://storage.googleapis.com/ezp-artifacts/python37-1.0.zip" %}

## 5. ML model push into **google bucket.**

you can collect models from git "  Insert Link".

Then create ezp/model directory and put models to that folder put models to that. Finally you can zip ezp folder and push into google bucket. 

## 6. Python Lib push into **google bucket.**

you can collect code from git "  Insert Link"

* Create folder "C:\Users\Apps\"
* Go inside that folder and open gitbash terminal and run below commands
* mkdir -p C:\Users\Apps\

```javascript
git clone <git link>

# Run Build Script
./pyarmour.sh
```

or double click  pyarmour.sh file

* Then go inside "C:\Users\Apps\ippylib\_pyarmor\library"
* Run following command

```javascript
pip install .
```

* Then copy C:\Users\Apps\ippylib\_pyarmor\library\ippylib to C:\Users\&lt;users&gt;\AppData\Roaming\Python\Python37\site-packages
* Copy site-packages to lib folder \(You can create lib folder anywhere\)  
* Then zip it and must send to the google bucket.

