# Setup Keycloak with SSL and MySQL backend

## Method 01

Keycloak is an open source identity and access management solution. Open Source Identity and Access Management  
For Modern Applications and Services. By default Keycloak uses an embedded H2 database. This should be sufficient for development on the local machine, but for production we will replace it with a MySQL standalone database

#### Standalone Installation

Simply, download Keycloak from the [Keycloak site](http://www.keycloak.org/downloads.html). After that extract/unzip to the requisite location. I prefer /opt for the installation. Even you can go with the [previous release](http://www.keycloak.org/downloads-archive.html). Keycloak comes in a few different flavors. There’s a standalone server that can be installed by simply extracting an archive. You can also start Keycloak on Docker or on OpenShift. You can also install Keycloak into an existing WildFly server.

Download and place mysql java connector to this location.

```text
cd /opt/keycloak-1.9.8.Final/modules/system/layers/keycloak/com/ mkdir -p mysql/main/ ls -ltrh total 984K -rw-r--r-- 1 ubuntu ubuntu 977K Sep 14 13:08 mysql-connector-java-5.1.44-bin.jar -rw-r--r-- 1 ubuntu ubuntu 282 Sep 14 13:12 module.xml # cat module.xml <?xml version="1.0" ?> <module xmlns="urn:jboss:module:1.3" name="com.mysql"> 	<resources> 		<resource-root path="mysql-connector-java-5.1.44-bin.jar" /> 	</resources> 	<dependencies> 		<module name="javax.api"/> <module name="javax.transaction.api"/> 	</dependencies>  </module>
```

#### Keycloak schema and User

Create a Keycloak schema and credentials to connect to MySQL Server.

#### Configurations

Place the keystore.jks file to this location. /opt/keycloak-1.9.8.Final/standalone/configuration

```text
# cd /opt/keycloak-1.9.8.Final/standalone/configuration
```

**MySQL connection details.**

`<datasource jndi-name="java:jboss/datasources/KeycloakDS" pool-name="KeycloakDS" enabled="true" use-java-context="true">  
<connection-url>jdbc:mysql://103.198.365.210:3306/keycloak?useSSL=false</connection-url>  
<driver>mysql</driver>  
<pool>  
<max-pool-size>20</max-pool-size>  
</pool>  
<security>  
<user-name>keycloak</user-name>  
<password>1wHNudifkherl9987f8i7p</password>  
</security>  
</datasource>`

`<datasource><drivers>  
<driver name="mysql" module="com.mysql">  
<xa-datasource-class>com.mysql.jdbc.Driver</xa-datasource-class>  
</driver>  
<driver name="h2" module="com.h2database.h2">  
<xa-datasource-class>org.h2.jdbcx.JdbcDataSource</xa-datasource-class>  
</driver>  
</drivers></datasource>`

**SSL settings**

`<security-realm name="UndertowRealm">  
<server-identities>  
<ssl>  
<keystore path="keystore.jks" relative-to="jboss.server.config.dir" keystore-password="fdkj439mdsfjoerm56yrfgcekzwbz" />  
</ssl>  
</server-identities>  
</security-realm></drivers></datasource>`

`<https-listener name="https" socket-binding="https" security-realm="UndertowRealm"/></security-realm></drivers></datasource>`

Setup Keycloak password with the below command.

`./bin/add-user-keycloak.sh -r master -u <username> -p <password>`

Start Keycloak Service.

`/bin/sh /opt/keycloak/bin/standalone.sh -b <private ip OR locahost>`

Browse http://127.0.0.1:8080/auth/ for GUI

## Method 02

We're going to run Keycloak on MySQL instead of the default H2 database. First, install the MySQL Yum repository.

```text
$ yum install -y https://dev.mysql.com/get/mysql80-community-release-el7-1.noarch.rpm
```

This will install the MySQL repository with the MySQL 8.0 repository enabled by default. Because Keycloak doesn't support MySQL 8.0 yet, disable the MySQL 8.0 repository and enable the MySQL 5.7 repository.

```text
$ yum-config-manager --disable mysql80-community$ yum-config-manager --enable mysql57-community
```

Install and start the MySQL server.

```text
$ yum install mysql-server$ systemctl start mysqld
```

The MySQL server generates a temporary password which can be found in the log file located in the _/var/log_ directory. Change this temporary password to something else before proceeding.

```text
$ mysqladmin -p password
```

Save the MySQL credentials in the home directory of the root user.

```text
$ cat > /root/.my.cnf <<EOF[client]user=rootpassword="YOURPASS"EOF
```

Create a database and user account for Keycloak \(change YOURPASS to your something random\).

```text
$ mysql mysql$ CREATE DATABASE keycloak;mysql$ CREATE USER 'keycloak'@'localhost' IDENTIFIED BY 'YOURPASS';mysql$ GRANT ALL PRIVILEGES ON keycloak.* TO 'keycloak'@'localhost';mysql$ FLUSH PRIVILEGES;mysql$ exit;
```

Configure Keycloak to use MySQL. Download the MySQL connector for Java.

```text
$ curl -L http://central.maven.org/maven2/mysql/mysql-connector-java/5.1.46/mysql-connector-java-5.1.46.jar -o /root/mysql-connector-java-5.1.46.jar
```

Open the Jboss CLI and add the MySQL module \(you don't have to connect with the Jboss websocket\).

```text
$ ./bin/jboss-cli.sh jboss-cli$ module add --name=org.mysql  --dependencies=javax.api,javax.transaction.api --resources=/root/mysql-connector-java-5.1.46.jarjboss-cli$ exit
```

Add the MySQL driver to the configuration.

```text
$ sudo -u keycloak ./bin/jboss-cli.sh 'embed-server,/subsystem=datasources/jdbc-driver=mysql:add(driver-name=mysql,driver-module-name=org.mysql,driver-class-name=com.mysql.jdbc.Driver)'
```

Remove the h2 KeycloakDS data source and add the MySQL KeycloakDS data source. \(Don't delete the test database and change YOURPASS to something random\)

```text
$ sudo -u keycloak ./bin/jboss-cli.sh 'embed-server,/subsystem=datasources/data-source=KeycloakDS:remove'$ sudo -u keycloak ./bin/jboss-cli.sh 'embed-server,/subsystem=datasources/data-source=KeycloakDS:add(driver-name=org.mysql,enabled=true,use-java-context=true,connection-url="jdbc:mysql://localhost:3306/keycloak?useSSL=false&amp;useLegacyDatetimeCode=false&amp;serverTimezone=Europe/Amsterdam&amp;characterEncoding=UTF-8",jndi-name="java:/jboss/datasources/KeycloakDS",user-name=keycloak,password="YOURPASS",valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.mysql.MySQLValidConnectionChecker,validate-on-match=true,exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.mysql.MySQLValidConnectionChecker)'
```

Add management user to keycloak \(change YOURPASS to your something random\).

```text
$ sudo -u keycloak ./bin/add-user-keycloak.sh -u admin -p YOURPASS -r master # output: Added 'admin' to '/opt/keycloak/4.5.0/standalone/configuration/keycloak-add-user.json', restart server to load user
```

We're going to run Keycloak behind a Nginx reverse proxy. To allow this, change http-listener and socket-binding configurations in Keycloak.

```text
$ sudo -u keycloak ./bin/jboss-cli.sh 'embed-server,/subsystem=undertow/server=default-server/http-listener=default:write-attribute(name=proxy-address-forwarding,value=true)'$ sudo -u keycloak ./bin/jboss-cli.sh 'embed-server,/socket-binding-group=standard-sockets/socket-binding=proxy-https:add(port=443)'$ sudo -u keycloak ./bin/jboss-cli.sh 'embed-server,/subsystem=undertow/server=default-server/http-listener=default:write-attribute(name=redirect-socket,value=proxy-https)'
```

Create a systemd configuration to start and stop keycloak using systemd.

```text
cat > /etc/systemd/system/keycloak.service <<EOF [Unit]Description=KeycloakAfter=network.target [Service]Type=idleUser=keycloakGroup=keycloakExecStart=/opt/keycloak/current/bin/standalone.sh -b 0.0.0.0TimeoutStartSec=600TimeoutStopSec=600 [Install]WantedBy=multi-user.targetEOF
```

Reload the systemd daemon and start Keycloak.

```text
$ systemctl daemon-reload$ systemctl enable keycloak$ systemctl start keycloak
```

Now, we'll install the Nginx server to do SSL termination for our Keycloak application. Install Nginx using the YUM package manager.

```text
$ yum install nginx
```

Copy the SSL certificates to the following paths on the server:

* Certificate: _/etc/pki/tls/certs/_
* Private key: _/etc/pki/tls/private/_

Configure Nginx to proxy traffic for Keycloak. \(Change [my.url.com](http://my.url.com/) to your own URL\)

```text
cat > /etc/nginx/conf.d/keycloak.conf <<EOFupstream keycloak {    # Use IP Hash for session persistence    ip_hash;      # List of Keycloak servers    server 127.0.0.1:8080;}        server {    listen 80;    server_name my.url.com;     # Redirect all HTTP to HTTPS    location / {         return 301 https://\$server_name\$request_uri;    }}  server {    listen 443 ssl http2;    server_name my.url.com;     ssl_certificate /etc/pki/tls/certs/my-cert.cer;    ssl_certificate_key /etc/pki/tls/private/my-key.key;    ssl_session_cache shared:SSL:1m;    ssl_prefer_server_ciphers on;     location / {      proxy_set_header Host $host;      proxy_set_header X-Real-IP $remote_addr;      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;      proxy_set_header X-Forwarded-Proto $scheme;      proxy_pass http://keycloak;    }}EOF
```

Enable and start Nginx.

```text
$ systemctl enable nginx$ systemctl start nginx
```

Open port 80 and 443 in your firewall and you're done!



{% embed url="https://hub.docker.com/r/jboss/keycloak/" %}

{% embed url="https://www.keycloak.org/downloads.html" %}

{% embed url="https://github.com/keycloak/keycloak-demo" %}



