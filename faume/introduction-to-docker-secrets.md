# Introduction to Docker Secrets

#### Prerequisites

**Docker** – See how to install Docker [here](https://docs.docker.com/engine/installation/).

**Swarm mode enabled -** Ensure that swarm mode is enabled before starting this tutorial. If it is not, open your terminal window and type the **docker swarm init** command**.**

#### No Secrets Are No Good

Before we start using secrets, let's look at the disadvantages of not using it. Below is a compose file with the definition of a Postgres  and adminer _\(a database client\)_ service:

```text
version: '3.1'
services:
  db:
    image: postgres
    environment:
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mysupersecretpassword
      POSTGRES_DB: mydatabase
  adminer:
    image: adminer
    ports:
     - 8080:8080
```

We have supplied the username, password, and database name for the Postgres service by setting the **POSTGRES\_USER**, **POSTGRES\_PASSWORD**, ****and **POSTGRES\_DB** environment variables _\(have a look_ Postgres _Docker image_ [_documentation_](https://hub.docker.com/_/postgres/) _to see other environment variables which can be used\)._

You may have noticed that the sensitive details for our database are in plain text for all the world to see. This is poor practice and should only be done for local deployments of containers. In the next section, we will be modifying this example to use secrets.

#### Securing Our Swarm With Secrets

Let's dive right in and see how to create secrets.

Open a terminal window and create a secret for the username by typing the following command:

```text
echo "myuser" | docker secret create pg_user -
```

We have used the **docker secret create** command above to create a secret called **pg\_user**. The dash "-" at the end of the command is important, it lets docker know that the data for the secret is being taken from stdin.

To view the secret, type the following command:

```text
docker secret ls
```

You should see a similar output displayed:

**ID                                                        NAME                CREATED       UPDATED**  
dv82o89ngapgr9lrxm6987uqh     pg\_user              5 days ago      5 days ago

Let's create the remaining secrets for password and database name:

```text
echo "mysupersecretpassword" | docker secret create pg_password -
```

```text
echo "mydatabase" | docker secret create pg_database -
```

Now we need to modify the compose file to use the secrets we created, see below:

```text
version: '3.1'
services:
    db:
        image: postgres
        restart: always
        environment:
            POSTGRES_USER_FILE: /run/secrets/pg_user
            POSTGRES_PASSWORD_FILE: /run/secrets/pg_password
            POSTGRES_DB_FILE: /run/secrets/pg_database
        secrets:
           - pg_password
           - pg_user
           - pg_database
    adminer: 
        image: adminer 
        ports: 
         - 8080:8080
secrets:
  pg_user:
    external: true
  pg_password:
    external: true
  pg_database:
    external: true
```

Create a file called **postgres-secrets.yml** and copy the text above to it. This file will be used later in this post to deploy our services.

We've added a few things to make our secrets work. Firstly, environment variables we used previously have been suffixed with  "**\_FILE."** The path to the secret is also specified, see below:

```text
POSTGRES_USER_FILE: /run/secrets/pg_user
POSTGRES_PASSWORD_FILE: /run/secrets/pg_password 

POSTGRES_DB_FILE: /run/secrets/pg_database
```

Docker secrets are stored in files under the **/run/secrets** folder of the container. This is why we have to specify new environment variables to read the secrets stored in these files.

**Important Note:** Not all images have environment variables which are compatible with secrets. I many cases you will have to modify the definition of images \(Dockerfile\) to read secrets.

Secondly, we specified the name of the secrets which are being used by the service:

```text
secrets:
- pg_password
- pg_user
- pg_database
```

And last but not least we indicated that the secrets are external:

```text
secrets:
 pg_user:
   external: true
 pg_password: 
   external: true
 pg_database: 
   external: true
```

#### Deploying the Service

Let's deploy our service to determine if our secrets are working as expected. Type the following command:

```text
docker stack deploy -c postgres-secrets.yml postgres
```

Give the services about a minute to fully start, and navigate to **http://127.0.0.1:8080/** to view the Adminer interface and enter the details as shown below:

![adminer1](https://denisdbell.files.wordpress.com/2017/11/adminer1.png)Click the **Login** button ****and you should see the admin interface displayed, see below:

![adminer2](https://denisdbell.files.wordpress.com/2017/11/adminer2.png)

If you see the screen above then our secrets are being stored and read by our Postgres service. Remember to always use secrets to improve the security of your docker services.

