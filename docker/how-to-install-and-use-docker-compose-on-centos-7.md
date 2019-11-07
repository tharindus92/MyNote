# How To Install and Use Docker Compose on CentOS 7

### [Install Docker Compose on CentOS](https://linuxize.com/post/how-to-install-and-use-docker-compose-on-centos-7/#install-docker-compose-on-centos) <a id="install-docker-compose-on-centos"></a>

The recommended method for installing Docker Compose on CentOS 7 is by downloading the Compose binary from the Docker’s GitHub repository.

At the time of writing this article, the latest stable version of Docker Compose is version `1.23.1`. Before downloading the Compose binary visit the [Compose repository release page on GitHub](https://github.com/docker/compose/releases) and check if there is a new version available for download.

Complete the following steps to install Docker Compose on CentOS 7:

1. Start by downloading the Docker Compose binary into the `/usr/local/bin` directory using the following [curl](https://linuxize.com/post/curl-command-examples/) command:

   ```text
   sudo curl -L "https://github.com/docker/compose/releases/download/1.23.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
   ```

   Copy

2. Once the download is complete, [make the binary executable](https://linuxize.com/post/chmod-command-in-linux/) by typing:

   ```text
   sudo chmod +x /usr/local/bin/docker-compose
   ```

   Copy

3. To verify the installation type the following command to print the Compose version:

   ```text
   docker-compose --version
   ```

   Copy

   The output will look something like this:

   ```text
   docker-compose version 1.23.1, build b02f1306
   ```

   Copy

### [Getting started with Docker Compose](https://linuxize.com/post/how-to-install-and-use-docker-compose-on-centos-7/#getting-started-with-docker-compose) <a id="getting-started-with-docker-compose"></a>

In this section, we’ll show how to use Docker Compose to run a WordPress stack on your CentOS 7 machine.

Start by [creating a new directory](https://linuxize.com/post/how-to-create-directories-in-linux-with-the-mkdir-command/) for the project and navigating into it:

```text
mkdir my_app && cd my_app
```

Copy

Next, [create a file](https://linuxize.com/post/create-a-file-in-linux/) named `docker-compose.yml` inside the project directory:

```text
nano docker-compose.yml
```

Copy

Paste the following content:



docker-compose.yml

```text
version: '3.3'

services:
  db:
    image: mysql:5.7
    restart: always
    volumes:
      - db_data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: password
      MYSQL_DATABASE: wordpress

  wordpress:
    image: wordpress
    restart: always
    volumes:
      - ./wp_data:/var/www/html
    ports:
      - "8080:80"
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_NAME: wordpress
      WORDPRESS_DB_USER: root
      WORDPRESS_DB_PASSWORD: password
    depends_on:
       - db

volumes:
    db_data:
    wp_data:
```

Copy

Let’s analyze the code line by line.

The first line specifies the [Compose file version](https://docs.docker.com/compose/compose-file/compose-versioning/). There are several different versions of the Compose file format with support for specific Docker releases.

Next, we are defining two services, `db` and `wordpress`. Each service runs one image and it will create a separate container when docker-compose is run.

The `db` service:

* Uses the [`mysql:5.7`](https://hub.docker.com/_/mysql/) image. In the image is not present on your system Compose will pull it from the Docker Hub public repository.
* Uses the restart `always` policy which will instruct the container to always restart.
* Creates a named volume `db_data` to make the database persistent.
* Defines the environment variables for the `mysql:5.7` image. 

The `wordpress` service:

* Uses the [`wordpress`](https://hub.docker.com/_/wordpress/) image. In the image is not present on your system Compose will pull it from the Docker Hub public repository.
* Uses the restart `always` policy which will instruct the container to always restart.
* Mounts the `wp_data` directory on the host to `/var/lib/mysql` inside the container.
* Forwards the exposed port 80 on the container to port 8080 on the host machine.
* Defines the [environment variables](https://linuxize.com/post/how-to-set-and-list-environment-variables-in-linux/) for the `wordpress` image.
* The `depends_on` instruction defines the dependency between the two services. In this example, `db` will be started before `wordpress`.

From the project directory, spin up the WordPress application using the following command:

```text
docker-compose up
```

Copy

The output should look something like this:

```text
...
wordpress_1  | [Sat Oct 13 21:30:48.286382 2018] [mpm_prefork:notice] [pid 1] AH00163: Apache/2.4.25 (Debian) PHP/7.2.10 configured -- resuming normal operations
wordpress_1  | [Sat Oct 13 21:30:48.286425 2018] [core:notice] [pid 1] AH00094: Command line: 'apache2 -D FOREGROUND'
```

Copy

Compose will pull both images, start two containers and create the `wp_data` directory in your project directory.

Open your browser, type `http://0.0.0.0:8080/` in and you will see the Wordpress installation screen.

At this point, the [WordPress application](https://linuxize.com/post/how-to-install-wordpress-with-apache-on-centos-7/) is up and running and you can start working on your theme or plugin.

To stop Compose press `CTRL+C`. environment variables If you want to start the Compose in a detached mode use the `-d` flag:

```text
docker-compose up -d
```

Copy

To check the running services use the `ps` option:

```text
docker-compose ps
```

Copy

```text
       Name                     Command               State          Ports        
----------------------------------------------------------------------------------
my_app_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp, 33060/tcp 
my_app_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:8080->80/tcp
```

Copy

When Compose is running in detached mode to stop the services use:

```text
docker-compose stop
```

Copy

To completely remove the containers use the `down` option:

```text
docker-compose down
```

Copy

Passing the `--volumes` switch will also remove the data volumes:

```text
docker-compose down --volumes
```

Copy

### [Uninstalling Docker Compose](https://linuxize.com/post/how-to-install-and-use-docker-compose-on-centos-7/#uninstalling-docker-compose) <a id="uninstalling-docker-compose"></a>

If you want to uninstall Docker Compose, simply [delete](https://linuxize.com/post/how-to-remove-files-and-directories-using-linux-command-line/) the binary using the following command:

```text
sudo rm /usr/local/bin/docker-compose
```

