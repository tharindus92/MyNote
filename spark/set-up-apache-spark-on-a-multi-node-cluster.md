# Set up Apache Spark on a Multi-Node Cluster



### Recommended Platform <a id="3ffe"></a>

**OS** - Linux is supported as development and deployment platform. Windows is supported as a dev platform.

For Apache Spark installation on a multi-node cluster, we will be needing multiple nodes, for that we can use multiple machines or AWS instances.

## Spark Architecture <a id="55f4"></a>

Apache Spark follows a master/slave architecture with two main daemons and a cluster manager –

* Master Daemon — \(Master/Driver Process\)
* Worker Daemon –\(Slave Process\)
* Cluster Manager

![](https://miro.medium.com/max/60/1*o80WzWJShjCV1RSfWzCiQA.png?q=20)![](https://miro.medium.com/max/668/1*o80WzWJShjCV1RSfWzCiQA.png)Spark Architecture

A spark cluster has a single Master and any number of Slaves/Workers. The driver and the executors run their individual Java processes and users can run them on the same horizontal spark cluster or on separate machines i.e. in a vertical spark cluster or in mixed machine configuration.

## Prerequisites <a id="a769"></a>

> Create a user of same name in master and all slaves to make your tasks easier during ssh and also switch to that user in master.

### Add entries in hosts file \(master and slaves\) <a id="e09d"></a>

Edit hosts file.

```text
$ sudo vim /etc/hosts
```

Now add entries of master and slaves in hosts file.

```text
<MASTER-IP> master
<SLAVE01-IP> slave01
<SLAVE02-IP> slave02
```

### Install Java 7 \(master and slaves\) <a id="a0c3"></a>

```text
$ sudo apt-get install python-software-properties
$ sudo add-apt-repository ppa:webupd8team/java
$ sudo apt-get update
$ sudo apt-get install oracle-java7-installer
```

To check if java is installed, run the following command.

```text
$ java -version
```

### Install Scala \(master and slaves\) <a id="a3a3"></a>

```text
$ sudo apt-get install scala
```

To check if scala is installed, run the following command.

```text
$ scala -version
```

### Configure SSH \(only master\) <a id="81da"></a>

**Install Open SSH Server-Client**

```text
$ sudo apt-get install openssh-server openssh-client
```

**Generate key pairs**

```text
$ ssh-keygen -t rsa -P ""
```

**Configure passwordless SSH**

Copy the content of _.ssh/id\_rsa.pub_ \(of master\) to _.ssh/authorized\_keys_ \(of all the slaves as well as master\).

**Check by SSH to all the slaves**

```text
$ ssh slave01
$ ssh slave02
```

## Install Spark <a id="1ae5"></a>

### Download latest version of Spark <a id="38c8"></a>

Use the following command to download latest version of apache spark.

```text
$ wget http://www-us.apache.org/dist/spark/spark-2.3.0/spark-2.3.0-bin-hadoop2.7.tgz
```

### Extract Spark tar <a id="c7ef"></a>

Use the following command for extracting the spark tar file.

```text
$ tar xvf spark-2.3.0-bin-hadoop2.7.tgz
```

### Move Spark software files <a id="cba5"></a>

Use the following command to move the spark software files to respective directory \(_/usr/local/bin_\)

```text
$ sudo mv spark-2.3.0-bin-hadoop2.7 /usr/local/spark
```

### Set up the environment for Spark <a id="96b2"></a>

Edit _bashrc_ file.

```text
$ sudo vim ~/.bashrc
```

Add the following line to _~**/.**bashrc_ file. It means adding the location, where the spark software file are located to the PATH variable.

```text
export PATH = $PATH:/usr/local/spark/bin
```

Use the following command for sourcing the _~/.bashrc_ file.

```text
$ source ~/.bashrc
```

> Note: The whole spark installation procedure must be done in master as well as in all slaves.

## Spark Master Configuration <a id="22bd"></a>

Do the following procedures only in master.

### Edit spark-env.sh <a id="49ca"></a>

Move to spark _conf_ folder and create a copy of template of _spark-env.sh_ and rename it.

```text
$ cd /usr/local/spark/conf
$ cp spark-env.sh.template spark-env.sh
```

Now edit the configuration file _spark-env.sh._

```text
$ sudo vim spark-env.sh
```

And set the following parameters.

```text
export SPARK_MASTER_HOST='<MASTER-IP>'export JAVA_HOME=<Path_of_JAVA_installation>
```

### Add Workers <a id="07fa"></a>

Edit the configuration file _slaves_ in \(_/usr/local/spark/con_f\).

```text
$ sudo vim slaves
```

And add the following entries.

```text
master
slave01
slave02
```

## Start Spark Cluster <a id="49b5"></a>

To start the spark cluster, run the following command on master.

```text
$ cd /usr/local/spark
$ ./sbin/start-all.sh
```

To stop the spark cluster, run the following command on master.

```text
$ cd /usr/local/spark
$ ./sbin/stop-all.sh
```

### Check whether services have been started <a id="e2f2"></a>

To check daemons on master and slaves, use the following command.

```text
$ jps
```

## Spark Web UI <a id="2ec0"></a>

Browse the Spark UI to know about worker nodes, running application, cluster resources.

### Spark Master UI <a id="51c2"></a>

```text
http://<MASTER-IP>:8080/
```

![](https://miro.medium.com/max/60/1*xly06f4vbsvSR0Nr0BK0AQ.png?q=20)![](https://miro.medium.com/max/2319/1*xly06f4vbsvSR0Nr0BK0AQ.png)

### Spark Application UI <a id="26f7"></a>

```text
http://<MASTER_IP>:4040/
```

![](https://miro.medium.com/max/60/1*Q8M2X0m-pcLR3p1eLZiUSQ.png?q=20)![](https://miro.medium.com/max/2320/1*Q8M2X0m-pcLR3p1eLZiUSQ.png)

>

