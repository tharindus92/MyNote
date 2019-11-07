# Insecure Registry to Docker Engine on windows server 2019



**PREPARE THE WINDOWS DOCKER MACHINE**

* Login into windows VM and Go-to **C:\programdata\docker\config** folder.

[![Steps to configure private registry for Docker Windows server 2016](https://www.assistanz.com/wp-content/uploads/2018/01/image_thumb-175.png)](https://www.assistanz.com/wp-content/uploads/2018/01/image-175.png)

* Create a new filename **daemon.json** in that folder.

[![Steps to configure private registry for Docker Windows server 2016](https://www.assistanz.com/wp-content/uploads/2018/01/image_thumb-176.png)](https://www.assistanz.com/wp-content/uploads/2018/01/image-176.png)

* Open the daemon.json file and add the below entry.

**{“insecure-registries”:\[“privaterepo:5000”\]}**

[![Steps to configure private registry for Docker Windows server 2016](https://www.assistanz.com/wp-content/uploads/2018/01/image_thumb-177.png)](https://www.assistanz.com/wp-content/uploads/2018/01/image-177.png)

* Save and close the file.

### Linux

 Change the docker push https connection to http. Whenever we use ‘docker push’ command it will try to make https connection to the registry server but in case of private registry server setup, it accepts only http connection from the client\(dkengine1.example.com\)

Edit the file “**/usr/lib/systemd/system/docker.service**” and change the parameter

ExecStart=/usr/bin/dockerd

to

ExecStart=/usr/bin/dockerd –insecure-registry docker-repo.example.com:5000





### Add Insecure Registry to Docker Engine

By default, docker uses **https** to connect to docker registry. But there can be use cases to use an insecure registry, especially if you’re on a trusted network. This eliminates the need for a CA-signed certificate for internal use or to trust self-signed certificate in all docker nodes. Here are the steps to add Insecure Registry to Docker Engine.

For Ubuntu Xenial, edit _/etc/docker/daemon.json_ and update the key “insecure-registries”. e.g.

```text
{ "insecure-registries" : ["myregistry.local:5000"] }
```

For CentOS 7, edit the file _/etc/docker/daemon.json_, e.g.

```text
{ "insecure-registries" : ["myregistry.local:5000"] }
```

For Ubuntu trusty, edit the file _/etc/default/docker_ and update _DOCKER\_OPTS_, e.g

```text
DOCKER_OPTS='--insecure-registry myregistry.local:5000'
```

Then restart Docker engine

```text
# systemctl restart docker
```

{% embed url="https://docs.docker.com/registry/insecure/" %}

```text
{  "insecure-registries" : ["10.101.15.111:5000"],  "allow-nondistributable-artifacts" : ["10.101.15.111:5000"]}
```

