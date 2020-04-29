# Helped guild

 [**install\_spark\_centos7.sh**](https://gist.github.com/darcyliu/d47edccb923b0f03280a4cf8b66227c1#file-install_spark_centos7-sh)

```text
#!/bin/bash
# Install Spark on CentOS 7
yum install java -y
java -version

yum install wget -y
wget http://downloads.typesafe.com/scala/2.11.7/scala-2.11.7.tgz
tar xvf scala-2.11.7.tgz
sudo mv scala-2.11.7 /usr/lib
sudo ln -s /usr/lib/scala-2.11.7 /usr/lib/scala
export PATH=$PATH:/usr/lib/scala/bin
scala -version

wget http://d3kbcqa49mib13.cloudfront.net/spark-1.6.0-bin-hadoop2.6.tgz
tar xvf spark-1.6.0-bin-hadoop2.6.tgz

export SPARK_HOME=$HOME/spark-1.6.0-bin-hadoop2.6
export PATH=$PATH:$SPARK_HOME/bin

firewall-cmd --permanent --zone=public --add-port=6066/tcp
firewall-cmd --permanent --zone=public --add-port=7077/tcp
firewall-cmd --permanent --zone=public --add-port=8080-8081/tcp
firewall-cmd --reload

echo 'export PATH=$PATH:/usr/lib/scala/bin' >> .bash_profile
echo 'export SPARK_HOME=$HOME/spark-1.6.0-bin-hadoop2.6' >> .bash_profile
echo 'export PATH=$PATH:$SPARK_HOME/bin' >> .bash_profile
```



**Step 3. Installing Scala.**

Spark installs Scala during the installation process, so we just need to make sure that Java and Python are present:

```text
wget http://www.scala-lang.org/files/archive/scala-2.10.1.tgz
tar xvf scala-2.10.1.tgz
sudo mv scala-2.10.1 /usr/lib
sudo ln -s /usr/lib/scala-2.10.1 /usr/lib/scala
export PATH=$PATH:/usr/lib/scala/bin
```

Once installed, check scala version:

```text
scala -version
```

**Step 4. Installing Apache Spark.**

Install Apache Spark using following command:

```text
wget http://www-eu.apache.org/dist/spark/spark-2.2.1/spark-2.2.1-bin-hadoop2.7.tgz
tar -xzf spark-2.2.1-bin-hadoop2.7.tgz
export SPARK_HOME=$HOME/spark-2.2.1-bin-hadoop2.7
export PATH=$PATH:$SPARK_HOME/bin
```

Setup some Environment variables before you start spark:

```text
echo 'export PATH=$PATH:/usr/lib/scala/bin' >> .bash_profile
echo 'export SPARK_HOME=$HOME/spark-1.6.0-bin-hadoop2.6' >> .bash_profile
echo 'export PATH=$PATH:$SPARK_HOME/bin' >> .bash_profile
```

The standalone Spark cluster can be started manually i.e. executing the start script on each node, or simple using the available launch scripts. For testing we can run master and slave daemons on the same machine:

```text
./sbin/start-master.sh
```

