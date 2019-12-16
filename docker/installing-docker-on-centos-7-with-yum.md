# Installing Docker on CentOS 7 with yum

nstalling from Docker repositories using the yum command is the easiest and most popular method.

#### Step 1: Update Docker Package Database

In a terminal window, type:

```text
sudo yum check-update
```

Allow the operation to complete.

#### Step 2: Install the dependencies

In a terminal window, enter the command:

```text
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

The **–y** switch indicates to the yum installer to answer “yes” to any prompts that may come up. The **yum-utils** switch adds the yum-config-manager. Docker uses a **device mapper** storage driver, and the **device-mapper-persistent-data** and **lvm2** packages are required for it to run correctly.

#### Step 3: Add the Docker repository to CentOS

In the terminal window, input the command:

```text
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

This command will add the Docker CE **stable** repository. You’ll need this repository, even if you want to install the **edge** or **test** versions of Docker.

A **stable** release is tested more thoroughly and has a slower update cycle. **Edge** release updates are more frequent but are not subjected to as many stability tests.

#### Step 4: Install Docker On CentOS using yum

In a terminal window, enter the command:

```text
sudo yum install docker
```

The system should begin the installation.

It is possible that your operating system will ask you to accept the GPG key, which is like a digital fingerprint, so you know whether to trust the installation.

The fingerprint should match the following format:

```text
060A 61C5 1B55 8A7F 742B 77AA C52F EB6B 621E 9F35
```

#### Step: 5 Manage Docker Service

You have installed Docker on CentOS, but the service is not running.

You can start the service, enable it to run at startup, or check the status of the service by running the following commands \(respectively\):

To start Docker:

```text
sudo systemctl start docker
```

To enable Docker:

```text
sudo systemctl enable docker
```

To check the status of Docker on CentOS:

```text
sudo systemctl status docker
```

### Install a Specific Version of Docker on CentOS

To **choose a specific version of Docker to install**, start by listing the available versions.

Type the following in your terminal window:

```text
yum list docker-ce --showduplicates | sort –r
```

The system should give you a list of different versions from the repositories you have enabled above.

Install the selected Docker version with the command:

```text
sudo yum install docker-ce-<VERSION STRING>
```

The **`<VERSION STRING>`** is found in the middle column and is the first part of the alphanumeric code before the hyphen.



Docker version 1.13.1

For CentOS Linux release 7.2.1511 \(Core\), ensure following `DOCKER_STORAGE_OPTIONS` option is set to `devicemapper`.

```text
bash # vi /etc/sysconfig/docker-storage
...
DOCKER_STORAGE_OPTIONS="--storage-driver devicemapper "
...

bash # vi /etc/sysconfig/docker-storage-setup
...
STORAGE_DRIVER=devicemapper
...
```

