# Install on windows server 2019



```text
Start-BitsTransfer -Source https://dockermsft.blob.core.windows.net/dockercontainer/docker-18-09-6.zip -Destination docker-18-09-6.zip Get-FileHash -Path docker-18-09-6.zip -Algorithm SHA256Install-Package -Name docker -ProviderName DockerMsftProvider -Verbose
```



### How to run Docker Containers on Windows Server 2019

Before you can use the Windows Containers to run multiple isolated applications your system, you’ll need to enable the containers feature and install Docker on Windows Server 2019.

#### Step 1: Enable the containers feature in Windows Server 2019

The first step is to enable the Windows Server 2019 containers feature. Open PowerShell as Administrator.![](https://computingforgeeks.com/wp-content/uploads/2019/02/run-docker-containers-windows-server-2019-01.png)

Run the following commands.

```text
Install-Module -Name DockerMsftProvider -Repository PSGallery -Force
```

This will install the Docker-Microsoft PackageManagement Provider from the [PowerShell Gallery. ](https://www.powershellgallery.com/packages/DockerMsftProvider)

Sample output is as shown below:

![](https://computingforgeeks.com/wp-content/uploads/2019/02/run-docker-containers-windows-server-2019-02.png)

#### Step 2: Install Docker on Windows Server 2019

once the Containers feature is enabled on Windows Server 2019, install the latest Docker Engine and Client by running the command below in your PowerShell session.

```text
Install-Package -Name docker -ProviderName DockerMsftProvider
```

Agree to the installation using “Yes” or “Y” or “A” to Agree to all.

![](https://computingforgeeks.com/wp-content/uploads/2019/02/run-docker-containers-windows-server-2019-03.png)

When the installation is complete, reboot the computer.

```text
Restart-Computer -Force
```

Installed Docker version can be checked with:

```text
Administrator> Get-Package -Name Docker -ProviderName DockerMsftProvider Name                           Version          Source                           ProviderName ----                           -------          ------                           ------------ docker                         18.09.2          DockerDefault                    DockerMsftProvider
```

The same can be achieved with the `docker --version` command.

```text
PS C:\Users\Administrator> docker version Client:  Version:           18.09.2  API version:       1.39  Go version:        go1.10.6  Git commit:        1ac774dfdd  Built:             unknown-buildtime  OS/Arch:           windows/amd64  Experimental:      false error during connect: Get http://%2F%2F.%2Fpipe%2Fdocker_engine/v1.39/version: open //./pipe/docker_engine: The system cannot find the file specified. In the default daemon configuration on Windows, the docker client must be run elevated to connect. This error may also indicate that the docker daemon is not running.
```

Upgrade can be done anytime by running the following commands on PowerShell.

```text
Install-Package -Name Docker -ProviderName DockerMsftProvider -Update -ForceStart-Service Docker
```

