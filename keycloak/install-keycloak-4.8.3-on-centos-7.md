# Install Keycloak 4.8.3 on CentOS 7

## Install

Basically follow the steps described in the [official guide](https://www.keycloak.org/docs/latest/server_installation/index.html#installation) .

### environment

* OS: CentOS 7.5



### System requirements

The system requirements for building Keycloak are as follows:

* Java 8 JDK
* RAM 512GB or more
* 1GB of disk space

### Install OpenJDK 8

 Red Hat Single Sign-On **requires Java 8 JDK** .  
If it is not installed, install the Java 8 JDK.  
\* In this document, OpenJDK 8 is used.

#### Check if Java is installed

Check if Java is installed in the environment.  
If Java 8 JDK is already installed, the following message is displayed.

```text
# java -versionopenjdk version "1.8.0_191"OpenJDK Runtime Environment (build 1.8.0_191-b12)OpenJDK 64-Bit Server VM (build 25.191-b12, mixed mode)
```

If Java 8 is not installed, install OpenJDK 8 using the following command.

```text
# yum install -y java-1.8.0-openjdk
```

### Download Keycloak

Download the latest Keycloak compressed file from the [official website](https://www.keycloak.org/downloads.html) .

### Unzip Keycloak compressed file

Unzip the downloaded Keycloak compressed file.  
\* The file is `/opt`placed in the following location.

```text
# cd / opt# unzip keycloak-4.8.3.Final.zip# mv keycloak-4.8.3.Final keycloak
```

### Firewall settings

Configure the firewall so that Keycloak can be accessed from the outside.

```text
# firewall-cmd --zone=public --add-service=http --permanent# firewall-cmd --zone=public --add-service=https --permanent# firewall-cmd --zone=public --add-port=8080/tcp --permanent# firewall-cmd --reload
```

### Creating an administrator user

Create an administrator user for Keycloak.

```text
# /opt/keycloak/bin/add-user-keycloak.sh -r master -u admin -p admin
```

### Starting Keycloak

```text
# /opt/keycloak/bin/standalone.sh -b=0.0.0.0
```

 The `-b=0.0.0.0`reason for adding the boot option is to allow access from all hosts.  
Keycloak comes with a Java EE container developed by RedHat called **WildFly** . FildFly **does not allow access from remote hosts by default.**

 Keycloak is running on top of this WildFly, so `-b`  ****Optionally specify hosts that are allowed access you need to.

If the following message is displayed after the command is executed, startup is successful.

```text
05: 22: 25,628 INFO [org.jboss.as] (Controller Boot Thread) WFLYSRV0060: Http management interface listening on http://127.0.0.1:9990/management05: 22: 25,628 INFO [org.jboss.as] (Controller Boot Thread) WFLYSRV0051: Admin console listening on http://127.0.0.1:999005: 22: 25,628 INFO [org.jboss.as] (Controller Boot Thread) WFLYSRV0025: Keycloak 4.8.3.Final (WildFly 
```

### Log in to the management console

If startup is successful, access the management console.  
`http://<host-name-or-IP>:8080/auth`

* _&lt;host-name-or-IP&gt;_ : OS host name or IP address

If access is successful, the following screen will be displayed. Select  
**Administration Console** to go to the login screen.  


![](../.gitbook/assets/image%20%286%29.png)

 **add-user-keycloak.sh** user name you entered at the time of execution, enter a password to log in to the Keycloak.  


![](../.gitbook/assets/image%20%281%29.png)

### Service registration

Set up Keycloak to start automatically when the server starts.  
`/etc/systemd/system/keycloak.service`The line 12 `-b option`is the same as above.

```text
[Unit]Description = Jboss Application ServerAfter = network.target[Service]Type = idleUser = keycloakGroup = keycloakExecStart = /opt/keycloak/bin/standalone.sh -b=0.0.0.0TimeoutStartSec = 600TimeoutStopSec = 600[Install]WantedBy = multi-user.target
```

```text
# mkdir /var/log/keycloak# useradd --system --user-group keycloak# chown -R keycloak: keycloak /opt/keycloak# chown -R keycloak: keycloak /var/log/keycloak# chmod -R g+w /opt/keycloak# chmod -R g+w /var/log/keycloak# systemctl daemon-reload# systemctl enable keycloak# systemctl start keycloak
```



