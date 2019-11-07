# Configure a Private Docker Registry on CentOS 7

[Docker](https://www.docker.com/) creates containers from images. These images are provided by [Docker Hub](https://hub.docker.com/), a centralized public registry that contains many official and unofficial images of almost every software in the world. However, there are situations, when we required to configure our on-premises **Private Docker Registry** to create and share custom docker images amongst our organizational units.

Private Docker Registry has many advantages vs Docker Hub, some of them are:

* Since, the Docker Registry is located on premises, therefore it increases availability and speed.
* Organization’s private images are kept within the Organizaion.
* Provides user authentication to restrict unauthorized access.

In this article, we will configure a Private Docker Registry on CentOS 7 for our on-premises Docker hosts.

Here, we are using the Docker Engine CE to configure a Private Docker Registry. Therefore,  it is advised that you should read [Docker Deep Dive](https://amzn.to/2Bzt4bn) for some basic to advance level understanding of Docker technology.

### System Specification:

We have provisioned a CentOS 7 virtual machine with following specifications:

| **Hostname:** | docker-01.example.com |
| :--- | :--- |
| **IP Address:** | 192.168.116.140/24 |
| **Operating System:** | CentOS 7.6 |
| **Docker Version:** | Docker CE 18 |

**Note:** Docker CE must be installed already on this server. You can follow our previous article [Install Docker CE on an Offline CentOS 7 Machine](https://ahmermansoor.blogspot.com/2019/02/install-docker-ce-on-offline-centos-7-machine.html).

### Configure TLS for Private Docker Registry:

We are planning to secure our Private Docker Registry with user authentication. Therefore, we are required to configure TLS \(Transport Layer Security\) first as a prerequisite for user authentication.

If you have [configured a Certificate Authority \(CA\)](https://ahmermansoor.blogspot.com/2019/01/configure-certificate-authority-ca-centos-7.html) for you network, then you can generate a Certificate Signing Request \(CSR\) and get your CSR signed by that CA \(Certificate Authority\).

However, for the sake of simplicity, we will generate a self-signed certificate in this article and import it in Docker hosts.

Connect to Docker host: docker-01.example.com and run following command to generate a self-signed digital certificate.

```text
[root@docker-01 ~]# mkdir -p /opt/docker/containers/docker-registry/certs[root@docker-01 ~]# openssl req > -newkey rsa:2048 \> -nodes -sha256 \> -x509 -days 365 \> -keyout /opt/docker/containers/docker-registry/certs/docker_registry.key \> -out /opt/docker/containers/docker-registry/certs/docker_registry.crtGenerating a 2048 bit RSA private key........................................................................+++........................+++writing new private key to 'docker_registry.key'-----You are about to be asked to enter information that will be incorporatedinto your certificate request.What you are about to enter is what is called a Distinguished Name or a DN.There are quite a few fields but you can leave some blankFor some fields there will be a default value,If you enter '.', the field will be left blank.-----Country Name (2 letter code) [XX]:PKState or Province Name (full name) []:SindhLocality Name (eg, city) [Default City]:KarachiOrganization Name (eg, company) [Default Company Ltd]:Ahmer's SysAdmin RecipesOrganizational Unit Name (eg, section) []:ITLABCommon Name (eg, your name or your server's hostname) []:docker-registry.example.comEmail Address []:root@docker-01.example.com
```

We have generated a self-signed digital certificate for our Private Docker Registry service. Hold it for a while, and we will use it later while creating the **registry** container for our Private Docker Registry.

### Configure Basic HTTP Authentication for Private Docker Registry:

We create a directory and then create a passwd file therein. we will mount this directory on **registry** container to implement basic HTTP authentication for our Private Docker Registry.

```text
[root@docker-01 ~]# mkdir -p /opt/docker/containers/docker-registry/auth[root@docker-01 ~]# docker run \> --entrypoint htpasswd \> registry -Bbn docker_user 123 > /opt/docker/containers/docker-registry/auth/htpasswd
```

### Create a Directory to persist Private Docker Registry Data:

Create a directory on Docker host. We will mount this directory in **registry** container and it will hold all data pertains to our Private Docker Registry.

```text
[root@docker-01 ~]# mkdir /opt/docker/containers/docker-registry/registry
```

By detaching this directory from **registry** container, we can easily reuse it with other containers derived from **registry**image. Therefore, if we remove our container, it won’t destroy the data within our Private Docker Registry.

### Create a Private Docker Registry Container on CentOS 7:

Pull **registry** image from [Docker Hub](https://hub.docker.com/).

```text
[root@docker-01 certs]# docker pull registryUsing default tag: latestlatest: Pulling from library/registryc87736221ed0: Pull complete1cc8e0bb44df: Pull complete54d33bcb37f5: Pull completee8afc091c171: Pull completeb4541f6d3db6: Pull completeDigest: sha256:3b00e5438ebd8835bcfa7bf5246445a6b57b9a50473e89c02ecc8e575be3ebb5Status: Downloaded newer image for registry:latest
```

Create a container for Private Docker Registry.

```text
[root@docker-01 ~]# docker run -d \> --name docker-registry \> --restart=always \> -p 5000:5000 \> -v /opt/docker/containers/docker-registry/registry:/var/lib/registry \> -v /opt/docker/containers/docker-registry/auth:/auth \> -e "REGISTRY_AUTH=htpasswd" \> -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" \> -e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd \> -v /opt/docker/containers/docker-registry/certs:/certs \> -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/docker-registry.crt \> -e REGISTRY_HTTP_TLS_KEY=/certs/docker-registry.key \> registryc1bf649e8277bc39133ab40c6338b3f07ea88f30628bb91060f33b77b3aeee0c
```

### Use Private Docker Registry on Docker Hosts:

We are adding the Private Docker Registry on the same Docker host docker-01.example.com on which we have created the registry container.

Add IP Address of Private Docker Registry to Local DNS resolver of Docker host.

```text
[root@docker-01 ~]# cat >> /etc/hosts << EOF> 172.17.0.2 docker-registry.example.com docker-registry> EOF
```

Install digital security certificate on Docker host as follow:

```text
[root@docker-01 ~]# mkdir -p /etc/docker/certs.d/docker-registry.example.com:5000[root@docker-01 ~]# cp /opt/docker/containers/docker-registry/certs/docker-registry.crt /etc/docker/certs.d/docker-registry.example.com:5000/ca.crt
```

Pull an image from Docker Hub. We will later push this image to our Private Docker Registry.

```text
[root@docker-01 ~]# docker pull busyboxUsing default tag: latestlatest: Pulling from library/busybox697743189b6d: Pull completeDigest: sha256:061ca9704a714ee3e8b80523ec720c64f6209ad3f97c0ff7cb9ec7d19f15149fStatus: Downloaded newer image for busybox:latest
```

Create another tag for busybox image, so we can push it into our Private Docker Registry.

```text
[root@docker-01 ~]# docker tag busybox:latest docker-registry.example.com:5000/busybox
```

Login to docker-registry.example.com using **docker** command.

```text
[root@docker-01 ~]# docker login docker-registry.example.com:5000Username: docker_userPassword:WARNING! Your password will be stored unencrypted in /root/.docker/config.json.Configure a credential helper to remove this warning. Seehttps://docs.docker.com/engine/reference/commandline/login/#credentials-storeLogin Succeeded
```

Push busybox image to Private Docker Registry.

```text
[root@docker-01 ~]# docker push docker-registry.example.com:5000/busyboxThe push refers to repository [docker-registry.example.com:5000/busybox]adab5d09ba79: Pushedlatest: digest: sha256:4415a904b1aca178c2450fd54928ab362825e863c0ad5452fd020e92f7a6a47e size: 527
```

List locally available images of busybox.

```text
[root@docker-01 ~]# docker images | grep busyboxbusybox                                    latest              d8233ab899d4        3 weeks ago         1.2MBdocker-registry.example.com:5000/busybox   latest              d8233ab899d4        3 weeks ago         1.2MB
```

You can see that busybox image is available from two different Docker Registries.

We can push as many images as we like into our Private Docker Registry by using the same procedure.

We have successfully configured a Private Docker Registry on CentOS 7.

```text
docker run -d --name docker-registry --restart=always -p 5000:5000 \-v /opt/docker/containers/docker-registry/registry:/var/lib/registry \-v /opt/docker/containers/docker-registry/auth:/auth \-e "REGISTRY_AUTH=htpasswd" \-e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" \-e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd \-v /opt/docker/containers/docker-registry/certs:/certs \-e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/docker-registry.crt  \-e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/docker-registry.crt \registrydocker run -d --name docker-registry --restart=always -p 5000:5000 \-v /opt/docker/containers/docker-registry/registry:/var/lib/registry \-v /opt/docker/containers/docker-registry/auth:/auth \-e "REGISTRY_AUTH=htpasswd" \-e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" \-e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd \-v /opt/docker/containers/docker-registry/certs:/certs \-e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/registry_auth.crt \-e REGISTRY_HTTP_TLS_KEY=/certs/registry_auth.key \registryopenssl req \-newkey rsa:2048 \-nodes -sha256 \-x509 -days 365 \-keyout /opt/docker/containers/docker-registry/certs/docker_registry.key \-out /opt/docker/containers/docker-registry/certs/docker_registry.crtopenssl req -newkey rsa:2048 -nodes -keyout registry_auth.key -x509 -days 365 -out registry_auth.crtdocker run -d --name docker-registry --restart=always -p 5000:5000 \-v /opt/docker/containers/docker-registry/registry:/var/lib/registry \-v /opt/docker/containers/docker-registry/auth:/auth \-e "REGISTRY_AUTH=htpasswd" \-e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" \-e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd \registrydocker tag ubuntu:16.04 docker-repo.example.com:5000/ubuntu:16.0docker push docker-repo.example.com:5000/ubuntu:16.04
```

{% embed url="https://docs.docker.com/engine/reference/commandline/login/\#credentials-store" %}



