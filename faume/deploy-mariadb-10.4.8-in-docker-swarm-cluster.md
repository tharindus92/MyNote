# Deploy MariaDB 10.4.8 in Docker Swarm Cluster

In this post I am going to show you how to deploy MariaDB in Docker Swarm Cluster using [docker compose](https://docs.docker.com/compose/) to act as database server for all applications we will be deploying later as I mentioned earlier in [Traefik](https://tuneit.me/traefik/cloud-native-edge-router-for-container-services/) article.

Contents

* [Prerequisite](https://tuneit.me/docker/deploy-mariadb-in-docker-swarm-cluster/#Prerequisite)
* [Introduction to MariaDB](https://tuneit.me/docker/deploy-mariadb-in-docker-swarm-cluster/#Introduction_to_MariaDB)
* [Docker Container Data](https://tuneit.me/docker/deploy-mariadb-in-docker-swarm-cluster/#Docker_Container_Data)
* [Secure MariaDB Environment](https://tuneit.me/docker/deploy-mariadb-in-docker-swarm-cluster/#Secure_MariaDB_Environment)
* [MariaDB Custom Configuration File](https://tuneit.me/docker/deploy-mariadb-in-docker-swarm-cluster/#MariaDB_Custom_Configuration_File)
* [MariaDB without Configuration File](https://tuneit.me/docker/deploy-mariadb-in-docker-swarm-cluster/#MariaDB_without_Configuration_File)
* [Prepare MariaDB Environment](https://tuneit.me/docker/deploy-mariadb-in-docker-swarm-cluster/#Prepare_MariaDB_Environment)
* [MariaDB Docker Compose](https://tuneit.me/docker/deploy-mariadb-in-docker-swarm-cluster/#MariaDB_Docker_Compose)
* [Deploy MariaDB using Docker Compose](https://tuneit.me/docker/deploy-mariadb-in-docker-swarm-cluster/#Deploy_MariaDB_using_Docker_Compose)
* [Create Databases](https://tuneit.me/docker/deploy-mariadb-in-docker-swarm-cluster/#Create_Databases)

**Prerequisite**

Please make sure you should fulfill below requirements before proceeding to the actual deployment.

1. [Docker Swarm Cluster](https://tuneit.me/docker/set-up-docker-swarm-cluster-on-ubuntu-18-in-azure/) with GlusterFS as persistent tool.
2. [Traefik](https://tuneit.me/docker/cloud-native-edge-router-for-container-services/) as reverse proxy to expose micro-services to external.

**Introduction to MariaDB**

[MariaDB](https://mariadb.org/) is one of the most popular database servers in the world. It’s fork of MySQL, made by the original developers of MySQL.

> MariaDB is guaranteed to stay open source. Notable users include Wikipedia, WordPress.com and Google.

MariaDB turns data into structured information in a wide array of applications, ranging from banking to websites. It is an enhanced, drop-in replacement for MySQL.

> MariaDB is used because it is fast, scalable and robust, with a rich ecosystem of storage engines, plugins and many other tools make it very versatile for a wide variety of use cases.

MariaDB is developed as open source software and as a relational database it provides an SQL interface for accessing data.

> The latest versions of MariaDB also include GIS and JSON features.

You can find more about MariaDB [here](https://mariadb.org/about/).

**Docker Container Data**

By default all data written to the file system inside a container is `ephemeral`. We will lost the data when the container is terminated. Web servers, application servers, proxies and a host of other applications do not store data directly themselves, relying instead on a database server for secure, reliable information storage.

Check [this](https://docs.docker.com/storage/) article to know about managing data in Docker Containers.

> In order to persist MariaDB data I am going to utilize GlusterFS Brick Replicated Volume that was created in [this article](https://tuneit.me/docker/set-up-docker-swarm-cluster-in-azure-on-ubuntu/)

**Secure MariaDB Environment**

MariaDB Docker images have typically offered various ways to set the MySQL root password, where some methods are recommended over others.

Ways to set passwords…

1. Specify `MYSQL_ROOT_PASSWORD` and `MYSQL_PASSWORD` in environment variable section in compose file.
2. Mounting a password file into the container and have `MYSQL_ROOT_PASSWORD`, `MYSQL_PASSWORD`, `MYSQL_ROOT_HOST`, `MYSQL_DATABASE` and `MYSQL_USER` in it.
3. Specify `MYSQL_RANDOM_ROOT_PASSWORD` in environment variable section to have MariaDB generate a random root password. The generated root password will be printed to stdout \(`GENERATED ROOT PASSWORD: .....`\). _**Recommended method**_
4. Docker Secrets – Secrets are securely stored in an encrypted Raft log and replicated to other nodes in the cluster. _**Recommended method**_

> We use Docker Secrets method for passing required password values to MariaDB Container.

Specifying `MYSQL_ROOT_PASSWORD` in environment variable is the least secure option. The environment variables are exposed to both the host system and to the container itself, leaving the password at very high risk of exposure.

Mounting a password file will avoid some of the exposure, but the file would still have to be stored on the host system. Not really ideal.

> Docker introduced a new mechanism for managing sensitive data with Docker Secrets in version 1.13. Click [here](https://docs.docker.com/engine/swarm/secrets/) to get an overview of it.

Setting up container with secrets in Docker Compose is straightforward. We’ll specify our secrets in environment variable section, and tell MariaDB to use those secrets after the container starts.

**MariaDB Custom Configuration File**

The startup configuration is specified in the file `/etc/mysql/my.cnf`, and that file in turn includes any files found in the `/etc/mysql/conf.d` directory that end with `.cnf`. Settings in files in this directory will augment and/or override settings in `/etc/mysql/my.cnf`. If you want to use a customized MySQL configuration, you can create your alternative configuration file in a directory on the host machine and then mount that directory location as `/etc/mysql/conf.d` inside the `mariadb` container.

> If `/my/custom/config-file.cnf` is the path and name of your custom configuration file, you can start your `mariadb` container with the directory path of the custom config file.

You have to mount only the directory path of the custom config file like command below:

```text
version: "3.7"
 
services:
  mariadb:
    image: mariadb:latest
    volumes:
      - /my/custom:/etc/mysql/conf.d
```

> This will start a new container `mariadb` where the MariaDB instance uses the combined startup settings from `/etc/mysql/my.cnf` and `/etc/mysql/conf.d/config-file.cnf` with settings from the latter taking precedence.

**MariaDB without Configuration File**

Many configuration options can be passed as flags to `mysqld`. This will give you the flexibility to customize the container without needing a `cnf` file.

> For example, if you want to change the default encoding and collation for all tables to use UTF-8 \(`utf8mb4`\). Also `wait_timeout,` `interactive_timeout` and `max_allowed_packet` settings for the container. Just include the values in `command` key as described in below code:

```text
version: "3.7"
 
services:
  mariadb:
    image: mariadb:latest
    command: ["mysqld", "--character-set-server=utf8mb4", "--collation-server=utf8mb4_unicode_ci", "--wait_timeout=28800", "--interactive_timeout=28800", "--max_allowed_packet=256M"]
```

> Please check [mariadb docker hub](https://hub.docker.com/_/mariadb) for more configuration options.

**Prepare MariaDB Environment**

Lets start creating passwords for MariaDB Container using Docker Secrets method.

`echo "{password}" | docker secret create wp_db_password`

`echo "{password}" | docker secret create mysql_root_password`

Create docker network, we will use it to bind MariaDB with Application Containers.

`docker network create --bridge private`

Create folder in `/mnt` directory to persistent MariaDB data folder, `/var/lib/mysql`

`cd /mnt`

`sudo mkdir -p mariadata`

**Please find below video for installing GlusterFS in Docker Swarm for data persistence**GlusterFS Replicated Volume

**MariaDB Docker Compose**

Now it’s time to create a folder, `maria` in `/opt` directory to place `docker compose` configuration file, i.e, `.yml` file for MariaDB.

Use the below commands to create the folder, `maria`.

`cd /opt`

`sudo mkdir -p maria`

`cd maria`

`sudo touch maria.yml`

Open `maria.yml` with nano editor using `sudo nano maria.yml`

Copy and paste below code in maria.yml configuration file.

Here is the `docker compose` file for MariaDB.

```text
version: "3.7"
 
services:
  mariadb:
    image: mariadb:latest
    volumes:
      - /mnt/mariadata:/var/lib/mysql
    secrets:
      - wp_db_password
      - mysql_root_password
    environment:
      - MYSQL_USER=testuser
      - MYSQL_DATABASE=testdb
      - MYSQL_PASSWORD_FILE=/run/secrets/wp_db_password
      - MYSQL_ROOT_PASSWORD_FILE=/run/secrets/mysql_root_password
    networks:
      - private
    deploy:
      placement:
        constraints: [node.role == manager]
      replicas: 1
      update_config:
        parallelism: 2
        delay: 10s
      restart_policy:
        condition: on-failure
      labels:
        - "traefik.enable=false"
secrets:
  wp_db_password:
    external: true
  mysql_root_password:
    external: true 
volumes:
  mariadata:
    driver: "local" 
networks:
  private:
    external: false
```

I use [Traefik](https://tuneit.me/docker/cloud-native-edge-router-for-container-services/) stack that was deployed \(`proxy stack`\) earlier to [Docker Swarm Cluster](https://tuneit.me/docker/set-up-docker-swarm-cluster-on-ubuntu-18-in-azure/) as a reverse proxy / load balancer. 

> Lets discuss about some of the configurations under `deploy` key.

deploy key

It allows us to specify configuration related to the deployment and running of services. The `deploy` key can also be used to constrain some services to run only on the manager node.

This only takes effect when deploying to a swarm cluster with \`docker stack deploy\` command and is ignored by `docker-compose up` and `docker-compose run` commands.

```text
deploy:
  mode: replicated
  replicas: 1
  update_config:
    parallelism: 2
    delay: 10s
  restart_policy:
    condition: on-failure
```

Several sub-options available for `deploy` key.

`mode` is specified either as `global` \(exactly one container per swarm node\) or `replicated` \(a specified number of containers\). Default is `replicated`.

mode: **global**

`replicas` is to specify the number of containers that should be running at any given time.

```text
replicas: 1
```

`placement` is to specify the placement constraints for the stack or service to run.

```text
placement:
  constraints:
    - node.role == manager
```

`update_config` is to configure how the service should be updated. Useful for configuring rolling updates.

* `parallelism`: The number of containers to update at a time.
* `delay`: The time to wait between updating a group of containers.
* `failure_action`: What to do if an update fails. Either `continue` or `pause` Default is `pause`.

```text
update_config: 
  parallelism: 2 
  delay: 10s
```

`resources` is to configure resource constraints. This replaces the older resource constraint options in Compose files prior to version 3 \(`cpu_shares`, `cpu_quota`, `cpuset`, `mem_limit`, `memswap_limit`\).

```text
resources:
  limits:
    cpus: '1.5'
    memory: 500M
  reservations:
    cpus: '1.0'
    memory: 200M
```

`restart_policy` is to configure whether and how to restart containers when they exit.

* `condition`: One of `none`, `on-failure` or `any`. _**Default is any**._
* `delay`: How long to wait between restart attempts. _**Default is 0**_.
* `max_attempts`: How many times to attempt to restart a container before giving up. _**Default is never give up**_.
* `window`: How long to wait before deciding if a restart has succeeded. _**Default is decide immediately**_.

```text
restart_policy: 
   condition: on-failure 
   delay: 5s 
   max_attempts: 3 
   window: 120s
```

`labels` is to specify labels for the service. These labels will be set only on the service, and _not_ on any containers for the service.

```text
version: "3.7"
services:
  mariadb:
    image: mariadb:latest
    deploy:
      labels:
        - "traefik.enable=false"
```

To set labels on containers instead, use the `labels` key outside of `deploy`:

```text
version: "3.7"

services:
  mariadb:
    image: mariadb:latest
    labels:
       - "traefik.enable=false" - "MariaDB stack is not available to outside by specifying false here"
```

**Deploy MariaDB using Docker Compose**

Now it’s time to deploy our `docker compose` file above, `maria.yml` using the below command

`docker stack deploy --compose-file maria.yml maria`

> You can give it any name for the stack. I just named it as `maria`

We can check the status of the stack by using `docker stack ps maria`

**Create Databases**

Login to MariaDB Container.

Type `docker ps` on `master node` to get the running containers on it. The out should look like below.![MariaDB Console](https://ml4ixtroomry.i.optimole.com/5RmTTnQ-J1igFphL/w:750/h:231/q:90/dpr:1.3/https://i2.wp.com/tuneit.me/wp-content/uploads/sites/4/2019/11/mariadb-1.png)MariaDB Console

Note down `CONTAINER ID` of MariaDB from the above command.

Type `docker exec -it CONTAINER ID bash` to log into MariaDB Container.

After logging into the container, type `mysql -u root -p` to go to MariaDB admin console.

Enter `root password` here, what ever you created using Docker Secrets method in Prepare Environment section of this article.

Just type `SHOW DATABASES` to see all the databases in our MariaDB Container.

> By default the container should create one database that was specified in the configuration file, `testdb` here.

We can create databases using `CREATE DATABASE test`;

> We will be using the databases for applications we are going to deploy soon.

Delete database using `DROP database`;

If you are not familiar with managing database server, MariaDB using console, I will post an article to deploy Adminer – Database Management Application which has excellent GUI to manage, written in single PHP file and has lot of advantages over phpMyAdmin tool.

Stay tuned for Adminer… 🙂

