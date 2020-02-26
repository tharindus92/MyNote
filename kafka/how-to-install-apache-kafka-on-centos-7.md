# How to Install Apache Kafka on CentOS 7

Apache Kafka is a scalable and high-throughtput messaging system which is capable of efficiently handling a huge amount of data.

You can either deploy Kafka on one server or build a distributed Kafka cluster for greater performance. As a starter, this article explains how to install Apache Kafka on one single Vultr CentOS 7 server instance.

#### Prerequisites: <a id="Prerequisites_"></a>

Before moving on, you should:

* Deploy a Vultr CentOS 7 server instance. Depending on your needs, you may need to increase the available memory.
* Use a sudo user to log in from your SSH terminal.

#### Step 1: Update the CentOS 7 system <a id="Step_1__Update_the_CentOS_7_system"></a>

Use the command below to update your system to the latest stable status:

```text
sudo yum update -y && sudo reboot
```

After the reboot has finished, use the same sudo user to log in again.

#### Step 2: Install OpenJDK Runtime <a id="Step_2__Install_OpenJDK_Runtime"></a>

You need to setup a Java virtual machine on your system before you can run Apache Kafka properly. Here, you can install OpenJDK Runtime Environment 1.8.0 using YUM:

```text
sudo yum install java-1.8.0-openjdk.x86_64
```

Validate your installation with:

```text
java -version
```

The output should resemble:

```text
openjdk version "1.8.0_91"
OpenJDK Runtime Environment (build 1.8.0_91-b14)
OpenJDK 64-Bit Server VM (build 25.91-b14, mixed mode)
```

You also need to setup the "JAVA\_HOME" and "JRE\_HOME" environment variables:

```text
sudo vi /etc/profile
```

Append the following lines to the original content of the file:

```text
export JAVA_HOME=/usr/lib/jvm/jre-1.8.0-openjdk
export JRE_HOME=/usr/lib/jvm/jre
```

Save and quit:

```text
:wq
```

Reload the profile to put your changes into effect:

```text
source /etc/profile
```





### Step 1 — Creating a User for Kafka <a id="step-1-&#x2014;-creating-a-user-for-kafka"></a>

Since Kafka can handle requests over a network, you should create a dedicated user for it. This minimizes damage to your CentOS machine should the Kafka server be compromised. We will create a dedicated **kafka** user in this step, but you should create a different non-root user to perform other tasks on this server once you have finished setting up Kafka.

Logged in as your non-root sudo user, create a user called **kafka** with the `useradd` command:

```text
sudo useradd kafka -m
```

The `-m` flag ensures that a home directory will be created for the user. This home directory, `/home/kafka`, will act as our workspace directory for executing commands in the sections below.

Set the password using `passwd`:

```text
sudo passwd kafka
```

Add the **kafka** user to the `wheel` group with the `adduser` command, so that it has the privileges required to install Kafka’s dependencies:

```text
sudo usermod -aG wheel kafka
```

Your **kafka** user is now ready. Log into this account using `su`:

```text
su -l kafka
```

Now that we’ve created the Kafka-specific user, we can move on to downloading and extracting the Kafka binaries.

### Step 2 — Downloading and Extracting the Kafka Binaries <a id="step-2-&#x2014;-downloading-and-extracting-the-kafka-binaries"></a>

Let’s download and extract the Kafka binaries into dedicated folders in our **kafka** user’s home directory.

To start, create a directory in `/home/kafka` called `Downloads` to store your downloads:

```text
mkdir ~/Downloads
```

Use `curl` to download the Kafka binaries:

```text
curl "https://www.apache.org/dist/kafka/2.1.1/kafka_2.11-2.1.1.tgz" -o ~/Downloads/kafka.tgz
```

Create a directory called `kafka` and change to this directory. This will be the base directory of the Kafka installation:

```text
mkdir ~/kafka && cd ~/kafka
```

Extract the archive you downloaded using the `tar` command:

```text
tar -xvzf ~/Downloads/kafka.tgz --strip 1
```

We specify the `--strip 1` flag to ensure that the archive’s contents are extracted in `~/kafka/` itself and not in another directory \(such as `~/kafka/kafka_2.11-2.1.1/`\) inside of it.

Now that we’ve downloaded and extracted the binaries successfully, we can move on configuring to Kafka to allow for topic deletion.

### Step 3 — Configuring the Kafka Server <a id="step-3-&#x2014;-configuring-the-kafka-server"></a>

Kafka’s default behavior will not allow us to delete a _topic_, the category, group, or feed name to which messages can be published. To modify this, let’s edit the configuration file.

Kafka’s configuration options are specified in `server.properties`. Open this file with `vi` or your favorite editor:

```text
vi ~/kafka/config/server.properties
```

Let’s add a setting that will allow us to delete Kafka topics. Press `i` to insert text, and add the following to the bottom of the file:~/kafka/config/server.properties

```text
delete.topic.enable = true
```

When you are finished, press `ESC` to exit insert mode and `:wq` to write the changes to the file and quit. Now that we’ve configured Kafka, we can move on to creating systemd unit files for running and enabling it on startup.

### Step 4 — Creating Systemd Unit Files and Starting the Kafka Server <a id="step-4-&#x2014;-creating-systemd-unit-files-and-starting-the-kafka-server"></a>

In this section, we will create [systemd unit files](https://www.digitalocean.com/community/tutorials/understanding-systemd-units-and-unit-files) for the Kafka service. This will help us perform common service actions such as starting, stopping, and restarting Kafka in a manner consistent with other Linux services.

Zookeeper is a service that Kafka uses to manage its cluster state and configurations. It is commonly used in many distributed systems as an integral component. If you would like to know more about it, visit the official [Zookeeper docs](https://zookeeper.apache.org/doc/current/index.html).

Create the unit file for `zookeeper`:

```text
sudo vi /etc/systemd/system/zookeeper.service
```

Enter the following unit definition into the file:/etc/systemd/system/zookeeper.service

```text
[Unit]
Requires=network.target remote-fs.target
After=network.target remote-fs.target

[Service]
Type=simple
User=kafka
ExecStart=/home/kafka/kafka/bin/zookeeper-server-start.sh /home/kafka/kafka/config/zookeeper.properties
ExecStop=/home/kafka/kafka/bin/zookeeper-server-stop.sh
Restart=on-abnormal

[Install]
WantedBy=multi-user.target
```

The `[Unit]` section specifies that Zookeeper requires networking and the filesystem to be ready before it can start.

The `[Service]` section specifies that systemd should use the `zookeeper-server-start.sh` and `zookeeper-server-stop.sh` shell files for starting and stopping the service. It also specifies that Zookeeper should be restarted automatically if it exits abnormally.

Save and close the file when you are finished editing.

Next, create the systemd service file for `kafka`:

```text
sudo vi /etc/systemd/system/kafka.service
```

Enter the following unit definition into the file:/etc/systemd/system/kafka.service

```text
[Unit]
Requires=zookeeper.service
After=zookeeper.service

[Service]
Type=simple
User=kafka
ExecStart=/bin/sh -c '/home/kafka/kafka/bin/kafka-server-start.sh /home/kafka/kafka/config/server.properties > /home/kafka/kafka/kafka.log 2>&1'
ExecStop=/home/kafka/kafka/bin/kafka-server-stop.sh
Restart=on-abnormal

[Install]
WantedBy=multi-user.target
```

The `[Unit]` section specifies that this unit file depends on `zookeeper.service`. This will ensure that `zookeeper` gets started automatically when the `kafa` service starts.

The `[Service]` section specifies that systemd should use the `kafka-server-start.sh` and `kafka-server-stop.sh` shell files for starting and stopping the service. It also specifies that Kafka should be restarted automatically if it exits abnormally.

Save and close the file when you are finished editing.

Now that the units have been defined, start Kafka with the following command:

```text
sudo systemctl start kafka
```

To ensure that the server has started successfully, check the journal logs for the `kafka` unit:

```text
journalctl -u kafka
```

You should see output similar to the following:

```text
OutputJul 17 18:38:59 kafka-centos systemd[1]: Started kafka.service.
```

You now have a Kafka server listening on port `9092`.

While we have started the `kafka` service, if we were to reboot our server, it would not be started automatically. To enable `kafka` on server boot, run:

```text
sudo systemctl enable kafka
```

Now that we’ve started and enabled the services, let’s check the installation.

