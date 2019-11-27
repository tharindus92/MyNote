# Creating Windows Service to Run an Executable Jar

**CAUTION: It is highly recommended to take registry backup and create a system restore point before following steps.**

**Prerequisites:**

i. Must have an executable jar.

ii. Must have admin permission to run command prompt in administrator mode.

iii. Must have “The Microsoft® Windows® Server 2003 Resource Kit Tools” installed.

\(Can download exe from here &gt;&gt;&gt; [https://www.microsoft.com/en-us/download/details.aspx?id=17657](https://www.microsoft.com/en-us/download/details.aspx?id=17657) \)

iv. Correct Java version to run the selected executable jar must be installed and system variables should be configured accordingly.

**Steps:**

1. Create folder in Windows machine which you need to setup service.

2. Copy executable jar in to the folder created in above step.

![Folder created and executable jar is copied.](https://miro.medium.com/max/1015/1*30GlYHH-6188_MYXBCDg1Q.png)



3. Find and copy path to “srvany.exe” with name and extension of the exe. This should be located inside “Windows Resource Kits\Tools” folder created after installing “The Microsoft® Windows® Server 2003 Resource Kit Tools”.

**e.g. /** “C:\Program Files \(x86\)\Windows Resource Kits\Tools\srvany.exe”

4. Open command prompt in administrator mode.

5. Execute following command in given order.

_**Command 1:**_

sc create **\[SERVICE\_NAME\]** binpath=**\[Path Copied in step 3\]** start=auto

**e.g. /** sc create “HttpCacheService” binpath=”C:\Program Files \(x86\)\Windows Resource Kits\Tools\srvany.exe” start=auto

_**Command 2:**_

reg add “HKLM\SYSTEM\CurrentControlSet\Services\**\[SERVICE\_NAME\]**\Parameters” /v Application /d **\[java command to run jar with absolute path to the jar\]**

**e.g. /** reg add “HKLM\SYSTEM\CurrentControlSet\Services\HttpCacheService\Parameters” /v Application /d “java -jar C:\HttpCacheService\HttpCacheService.jar”

_**Command 3:**_

sc start **\[SERVICE\_NAME\]**

**e.g. /** sc start “HttpCacheService”

After following above steps, newly create service should be available in Services window and should be up & running. Later you can change service to start manually.Example service “HttpCacheService” created is up and running.Later you can change service to start manually.

![Example service &#x201C;HttpCacheService&#x201D; created is up and running.](https://miro.medium.com/max/1094/1*dJPlTkcMkvPE45f2rmIPwA.png)

Also in Registry Editor you can see entries created for new service.

![Registry entry created for newly created service &#x201C;HttpCacheService&#x201D;.](https://miro.medium.com/max/969/1*EYvgdV-fqo8G6JDN3b5Zig.png)

Note that values inside “Parameters” key. It should be the command to run jar file in command prompt with absolute path to executable jar given at step 5 _Command 2_.

![Values inside &#x201C;Parameters&#x201D; key created for &#x201C;HttpCacheService&#x201D;.](https://miro.medium.com/max/901/1*8oWFU2uXFe3qmUSMOAUB9Q.png)



