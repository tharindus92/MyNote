# Installing Oracle Database XE Using RPM Packages

{% embed url="https://docs.oracle.com/en/database/oracle/oracle-database/18/xeinl/procedure-installing-oracle-database-xe.html" %}



Perform the following steps to install and configure Oracle Database XE using RPM packages.

Before attempting to install Oracle Database XE 18c, uninstall any existing Oracle Database XE or database with the SID `XE` from the target system.An Oracle Database XE installation will consume around 9 gigabytes of disk space under `/opt`. If this disk partition does not have the required disk space available, you must add space or mount an alternative partition as `/opt/oracle`. This disk partition is the defined Oracle Base where the software and database will reside.

Note:

The Oracle Database XE installation does not support usage of symbolic links \(symlink\) for that disk.

Installing Oracle Database XE RPM

1. Execute as user `root` using `sudo`.

   ```text
   sudo -s
   ```

2. For Oracle Linux, the Database Preinstallation RPM is pulled automatically, proceed to the next step. For Red Hat compatible Linux distributions, download and install the Database Preinstallation RPM using the following:
   1. For Red Hat Enterprise Linux 6, run these commands:

      ```text
      curl -o oracle-database-preinstall-18c-1.0-1.el6.x86_64.rpm https://yum.oracle.com/repo/OracleLinux/OL6/latest/x86_64/getPackage/oracle-database-preinstall-18c-1.0-1.el6.x86_64.rpm
      yum -y localinstall oracle-database-preinstall-18c-1.0-1.el6.x86_64.rpm
      ```

      For Red Hat Enterprise Linux 7, run these commands:

      ```text
      curl -o oracle-database-preinstall-18c-1.0-1.el7.x86_64.rpm https://yum.oracle.com/repo/OracleLinux/OL7/latest/x86_64/getPackage/oracle-database-preinstall-18c-1.0-1.el7.x86_64.rpm
      yum -y localinstall oracle-database-preinstall-18c-1.0-1.el7.x86_64.rpm
      ```

      Note:Use the `-y` option if you want `yum` to skip the package confirmation prompt.
3. Access the software download page for Oracle Database RPM-based installation from Oracle Technology Network :

   [https://www.oracle.com/technetwork/database/database-technologies/express-edition/downloads/index.html](https://www.oracle.com/technetwork/database/database-technologies/express-edition/downloads/index.html)

4. Download the `oracle-database-xe-18c-1.0-1.x86_64.rpm` file required for performing an RPM-based installation to a directory of your choice.
5. Install the database software using the `yum localinstall` command.

   ```text
   yum -y localinstall oracle-database-xe-18c-1.0-1.x86_64.rpm
   ```

The Database Preinstallation RPM automatically creates Oracle installation owner and groups and sets up other kernel configuration settings as required for Oracle installations. If you plan to use job-role separation, then create the extended set of database users and groups depending on your requirements. Check the RPM log file to review the system configuration changes.

For example, review this file for latest changes: `/var/log/oracle-database-preinstall-18c/results/orakernel.log` .

The installation of Oracle Database software is now complete.

After successful installation, you can delete the downloaded RPM files, for example:

```text
rm oracle-database-preinstall-18c-1.0-1.el6.x86_64.rpm
rm oracle-database-preinstall-18c-1.0-1.el7.x86_64.rpm
rm oracle-database-xe-18c-1.0-1.x86_64.rpm
```

Creating and Configuring an Oracle Database

The configuration script creates a container database \(`XE`\) with one pluggable database \(`XEPDB1`\) and configures the listener at the default port \(1521\) and Enterprise Manager Express on port 5500.

You can modify the configuration parameters by editing the `/etc/sysconfig/oracle—xe–18c.conf` file.The parameters set in this file are explained in more details in the silent mode installation procedure: [Performing a Silent Installation](https://docs.oracle.com/en/database/oracle/oracle-database/18/xeinl/performing-silent-installation.html).

To create the Oracle XE database with the default settings, perform the following steps:

1. Execute as user `root` using `sudo`.

   ```text
   sudo -s
   ```

2. Run the service configuration script:

   ```text
   /etc/init.d/oracle-xe-18c configure
   ```

   At the prompt, specify a password for the `SYS`, `SYSTEM`, and `PDBADMIN` administrative user accounts. Oracle recommends that the password entered should be at least 8 characters in length, contain at least 1 uppercase character, 1 lower case character and 1 digit \[0-9\].

   See Also:The same password will be used for these accounts. The password should conform to the Oracle recommended standards. See [Oracle Database Security Guide](https://www.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/18/xeinl&id=DBSEG-GUID-451679EB-8676-47E6-82A6-DF025FD65156#GUID-451679EB-8676-47E6-82A6-DF025FD65156/DBSEG498) for more information about guidelines for securing passwords

   After the configuration completes, the database and listener are started.

Configuration, Database Files and Logs Location

Table 5-1 Configuration, Database Files and Logs Location

| File Name and Location | Purpose |
| :--- | :--- |
| `/opt/oracle` | Oracle Base. This is the root of the Oracle Database XE directory tree. |
| `/opt/oracle/product/18c/dbhomeXE` | Oracle Home. This home is where the Oracle Database XE is installed. It contains the directories of the Oracle Database XE executables and network files. |
| `/opt/oracle/oradata/XE` | Database files. |
| `/opt/oracle/diag` subdirectories | Diagnostic logs. The database alert log is `/opt/oracle/diag/rdbms/xe/XE/trace/alert_XE.log` |
| `/opt/oracle/cfgtoollogs/dbca/XE` | Database creation logs. The `XE.log` file contains the results of the database creation script execution. |
| `/etc/sysconfig/oracle-xe-18c.conf` | Configuration default parameters. |
| `/etc/init.d/oracle-xe—18c` | Configuration and services script. |





## Performing a Silent Installation

You can install Oracle Express Edition using the silent mode. This mode can be used for embedded install of XE \(with your application\) or unattended operation.

To perform a silent installation, a password for the administrative accounts must be provided as a parameter to the script, or specified in the configuration file.

1. Create a wrapper shell script to perform the silent installation.

   It should contain commands similar to the following:

   ```text
   !/bin/bash
 
   yum -y localinstall /downloads/oracle-database-xe–18c-1.0-1.x86_64.rpm > /xe_logs/XEsilentinstall.log 2>&1

   /etc/init.d/oracle-xe-18c configure >> /xe_logs/XEsilentinstall.log 2>&1
   ```

   Alternatively, you can pass on the password in the script, such as:

   ```text
   (echo "password"; echo "password";) | /etc/init.d/oracle-xe-18c configure >> /xe_logs/XEsilentinstall.log 2>&1
   ```

   Replace password with a password that is secure. The password entered should be at least 8 characters in length, contain at least 1 uppercase character, 1 lower case character and 1 digit \[0-9\].

2. Make the wrapper script executable.

   ```text
   chmod +x myscript.sh
   ```

3. Run as `root` using `sudo`.

   ```text
   sudo ./myscript.sh
   ```

   XE Oracle Home is `/opt/oracle/product/18c/dbhomeXE` .

   For details of the installation, review the `/xe_logs/XEsilentinstall.log` file.

The XE database is configured with default settings. It is not necessary to modify these parameters unless you have specific requirements. Make a copy of the configuration file `/etc/sysconfig/oracle-xe-18c.conf` before modifying it. Make your modifications after the RPM install and before configuring the database.

The provided configuration file `/etc/sysconfig/oracle-xe-18c.conf` sets the following:

* `LISTENER_PORT`: A valid listener numeric port value for the database listener. Do not specify for automatic port assignment.
* `EM_EXPRESS_PORT`: A valid port numeric value for Oracle Enterprise Manager \(EM\) Express listener. This is set to port 5500.
* `CHARSET` : Character set of the database. This is set to `AL32UTF8`.
* `DBFILE_DEST` Database file directory. By default, the database files are stored in the Oracle Base /opt/oradata subdirectory.
* `SKIP_VALIDATIONS`: Skip validation for memory and disk space. Default: false.
* `ORACLE_PASSWORD`: The password for the administrative accounts.

Caution:

When you modify and save a file containing plain text password, ownership of the file should be given to the Oracle software installation owner only, and permissions on the file should be changed to 600. Oracle strongly recommends that database administrators or other administrators delete or secure such files containing plain text passwords when they are not in use.

Note:The password should conform to the Oracle recommended standards. See [Oracle Database Security Guide](https://www.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/18/xeinl&id=DBSEG-GUID-451679EB-8676-47E6-82A6-DF025FD65156#GUID-451679EB-8676-47E6-82A6-DF025FD65156/DBSEG498) for more information about guidelines for securing passwords

You can find the database creation logs under the Oracle Base `/opt/oracle/cfgtoollogs/dbca/` subdirectory.

See Also:[Configuration, Database Files and Logs Location](https://docs.oracle.com/en/database/oracle/oracle-database/18/xeinl/procedure-installing-oracle-database-xe.html#GUID-46EA860A-AAC4-453F-8EEE-42CC55A4FAD5__CONFIGURATIONDATABASEFILESANDLOGSLO-D33FB357) for a summary of important files and their locations



## Setting the Oracle Database XE Environment Variables



After you have installed and configured Oracle Database XE, you must set the environment before using Oracle Database XE.

The oraenv and coraenv scripts can be used to set your environment variables. 

For example, to set your environment variables in Bourne, Bash, or Korn shell without being prompted by the script:

```text
export ORACLE_SID=XE 
export ORAENV_ASK=NO 
. /opt/oracle/product/18c/dbhomeXE/bin/oraenv

ORACLE_HOME = [] ? /opt/oracle/product/18c/dbhomeXE
The Oracle base has been set to /opt/oracle
```

For C shell:

```text
setenv ORACLE_SID XE
setenv ORACLEENV_ASK NO
source /opt/oracle/product/18c/dbhomeXE/bin/coraenv
```

## Connecting to Oracle Database XE



Connecting using `SQL*Net`

The database listener for your XE database is started with a configuration that can be viewed using the command :

```text
lsnrctl status
```

```text
CopyConnecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=dbhost.example.com)(PORT=1521)))
STATUS of the LISTENER
------------------------
Alias                     LISTENER
Version                   TNSLSNR for Linux: Version 18.0.0.0.0 - Production
Trace Level               off
Security                  ON: Local OS Authentication
SNMP                      OFF
Default Service           XE
Listener Parameter File   /opt/oracle/product/18c/dbhomeXE/network/admin/listener.ora
Listener Log File         /opt/oracle/diag/tnslsnr/dbhost/listener/alert/log.xml
Listening Endpoints Summary...
  (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=dbhost.example.com)(PORT=1521)))
  (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))
  (DESCRIPTION=(ADDRESS=(PROTOCOL=tcps)(HOST=dbhost.example.com)(PORT=5500))(Security=(my_wallet_directory=/opt/oracle/admin/XE/xdb_wallet))(Presentation=HTTP)(Session=RAW))
Services Summary...
Service "77f81bd10c818208e053410cc40aef5a" has 1 instance(s).
  Instance "XE", status READY, has 1 handler(s) for this service...
Service "XE" has 1 instance(s).
  Instance "XE", status READY, has 1 handler(s) for this service...
Service "XEXDB" has 1 instance(s).
  Instance "XE", status READY, has 1 handler(s) for this service...
Service "xepdb1" has 1 instance(s).
  Instance "XE", status READY, has 1 handler(s) for this service...
The command completed successfully
```

For example, you can connect to the database from a client computer with `SQL*plus` using the connect identifier:

```text
sqlplus system@"dbhost.example/XE"
```

The `XE` services are defined in the configuration in `/opt/oracle/product/18c/dbhomeXE/network/admin/tnsnames.ora` file.

Connecting to Oracle Using Easy Connect Naming MethodYou can connect to the database using the following Easy Connect strings:

* Multitenant container database: host\[:port\]
* Pluggable database: host\[:port\]/service\_name

  XEPDB1 is the service name defined for the first PDB created by default. If your PDB has another name, you must provide the service name for that PDB.

## Starting and Stopping Oracle Database



ou can start and stop the database manually, set it to automatically after the system shuts down and starts, or using Enterprise Manager.

Shutting Down and Starting Up Using the Configuration Services Script

Execute these commands as `root` using `sudo`.

```text
sudo -s
```

Oracle Linux 6:

You can start and stop the database using the `/etc/init.d/oracle-xe-18c` script.

Run the following command to start the listener and database:

```text
/etc/init.d/oracle-xe-18c start
```

Run the following command to stop the database and the listener:

```text
/etc/init.d/oracle-xe-18c stop
```

Run the following command to stop and start the listener and database:

```text
/etc/init.d/oracle-xe-18c restart
```

Oracle Linux 7:

Run the following command to start the listener and database:

```text
systemctl start oracle-xe-18c
```

Run the following command to stop the database and the listener:

```text
systemctl stop oracle-xe-18c
```

Run the following command to stop and start the listener and database:

```text
systemctl restart oracle-xe-18c
```

Shutting Down and Starting Up Using SQL\*Plus

You can shut down and start the database using SQL\*Plus.

To shutdown the database, login to the oracle user with its environment variables set for access to the XE database, and issue the following SQL\*Plus command:

```text
sqlplus / as sysdba
SQL> SHUTDOWN IMMEDIATE
```

To start the database, issue the commands:

```text
SQL> STARTUP
SQL> ALTER PLUGGABLE DATABASE ALL OPEN; 
```

See Also:

* [Oracle Database 2 Day DBA ](https://www.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/18/xeinl&id=ADMQS-GUID-CE7EF896-BB8A-4D4F-869C-BF4FE0D85470)for general information about managing a database
* [Oracle Multitenant Administration Guide](https://www.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/18/xeinl&id=MULTI-GUID-94AD2899-CDC1-446E-9C8B-46497BEA4A18) for more information about shutting down and starting a PDB

Automating Shutdown and Startup

Oracle recommends that you configure the system to automatically start Oracle Database when the system starts, and to automatically shut it down when the system shuts down. Automating database shutdown guards against incorrect database shutdown.To automate the startup and shutdown of the listener and database, execute the following commands as `root`:

```text
sudo -s
```

For Oracle Linux 6, run these commands:

```text
/sbin/chkconfig oracle-xe-18c on
# /sbin/service oracle-xe-18c start
```

For Oracle Linux 7, run these commands:

```text
Copy# systemctl daemon-reload
# systemctl enable oracle-xe-18c
```

{% embed url="https://www.oracle.com/database/technologies/xe-downloads.html" %}

{% embed url="https://www.centlinux.com/2019/03/install-oracle-database-18c-on-centos-7.html" %}

[https://community.oracle.com/tech/apps-infra/discussion/3912361/sqlplus-command-not-found-on-linux](https://community.oracle.com/tech/apps-infra/discussion/3912361/sqlplus-command-not-found-on-linux)

