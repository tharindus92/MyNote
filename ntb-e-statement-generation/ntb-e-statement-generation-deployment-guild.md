# NTB - E-Statement Generation Deployment Guild

## **1. Deployment View**

This subsection describes the deployment view of the system. 

![](https://lh6.googleusercontent.com/6e0j70QWOqECjpr1MVzdgKRrTpHrcxYmjMTyVCVdHOZYkvunNnRsSH_J9VVeLt6jQYlMJXkXkgO1HsbHyRpma5xkwx1q9dyDR1spA-KU-usGjvW8tDs5VNraZj617KB7AckndCUj)

## 2 Deployment Guild

There is 4 service we deployed to server

1. Ntbes File Generation Worker Service
2.  Ntbes Data Processing Worker Service
3. Ntbes Email Sending Worker Service
4. Ntbes Admin Portal UI Service
5. 
### 2.1 Minimum Server Requirement 

### 2.2 Server Update

```bash
# update server
sudo yum update -y
```

### 2.3 Docker Installation

We are using docker swarm mode to deploy NTB - E-Statement Generation application. Then first we have to install docker.

```bash
#Install Docker

sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install docker-ce docker-ce-cli containerd.io
sudo yum install vim net-tools chrony wget -y
sudo systemctl start docker
sudo systemctl enable docker
systemctl status docker
```



### 2.4 Docker swarm init

```bash
docker swarm init  --default-addr-pool 172.81.0.0/16
```

To connect swarm master use following commands on worker

```text
docker swarm join-token worker
```

You can get following output

```text
To add a worker to this swarm, run the following command:

    docker swarm join \
    --token SWMTKN-1-3pu6hszjas19xyp7ghgosyx9k8atbfcr8p2is99znpy26u2lkl-1awxwuwd3z9j1z3puu7rcgdbx \
    172.17.0.2:2377
```

from above command you can join worker to manager.

### 2.5 Docker stack deploy

#### 2.5.1 Collect applications artifact \(TAR files\)  from zone24x7 team and copy it into the server. Then execute the following commands in order to load docker images.

```bash
sudo docker load -i ntbes_admin-portal-ui.tar 
sudo docker load -i ntbes_email_sending_worker.tar 
sudo docker load -i ntbes_data_processing_worker.tar 
sudo docker load -i ntbes_file_generation_worker.tar 
```

#### 2.5.2 Create folder structure for email sending worker

```bash
mkdir -p /opt/ntbes/email/
```

copy "**consolidate-interactive-statement.html**" to above location.

#### 2.5.3 Write prod.yml

```bash
cat > prod.yml <<EOF
version: '3.8'
services:
 ntbes-statement-generator:
  image: nexus.zone24x7.lk/ntbes/ntbes-statement-generator:1.0.0-snapshot-202106041231
  ports:
    - '8081:8081'
  networks:
      - ntbes
  deploy:
    mode: replicated
    replicas: 1
    placement:
      max_replicas_per_node: 1
    resources:
      limits:
        cpus: "1"
        memory: 1gb

 ntbes_file_generation_worker:
  image: nexus.zone24x7.lk/ntbes/ntbes_file_generation_worker:1.0.0-20210628.095650-28
  environment:
    DB_URL_ES: "jdbc:oracle:thin:@10.101.15.23:1521:XE"
    DB_USERNAME_ES: "NTB_ES_DEV"
    DB_PASSWORD_ES: 'Zone#12$$5S7'
  ports:
    - '8085:8085'
  networks:
      - ntbes
  deploy:
    mode: replicated
    replicas: 1
    placement:
      max_replicas_per_node: 1
    resources:
      limits:
        cpus: "1"
        memory: 1gb
        
 ntbes_data_processing_worker:
  image: nexus.zone24x7.lk/ntbes/ntbes_data_processing_worker:1.0.0-20210628.095558-28
  environment:
    DB_URL_ES: "jdbc:oracle:thin:@10.101.15.23:1521:XE"
    DB_USERNAME_ES: "NTB_ES_DEV"
    DB_PASSWORD_ES: 'Zone#12$$5S7'
    DB_URL_ET: "jdbc:oracle:thin:@10.101.15.23:1521:XE"
    DB_USERNAME_ET: "NTB_ETL_DEV"
    DB_PASSWORD_ET: 'Zone#12$$5S7'
  ports:
    - '8086:8086'
  networks:
      - ntbes
  deploy:
    mode: replicated
    replicas: 1
    placement:
      max_replicas_per_node: 1
    resources:
      limits:
        cpus: "1"
        memory: 1gb
        
 ntbes_email_sending_worker:
  image: nexus.zone24x7.lk/ntbes/ntbes_email_sending_worker:1.0.0-20210702.061228-33
  ports:
    - '8083:8083'
  networks:
      - ntbes
  volumes:      
      - '/opt/ntbes/email/:/var/statements/'
  deploy:
    mode: replicated
    replicas: 1
    placement:
      max_replicas_per_node: 1
    resources:
      limits:
        cpus: "1"
        memory: 1gb
        
 ntbes_admin-portal-ui:
  image: nexus.zone24x7.lk/ntbes/admin-portal-ui:1.0.0-202107060733
  environment:
    FRONTEND_HOSTNAME: 'adminporatal.dev.zone24x7.lk'
  ports:
    - '443:8443'
  networks:
      - ntbes
  volumes:      
      - '/opt/ntbes/email/:/var/statements/'
  deploy:
    mode: replicated
    replicas: 1
    placement:
      max_replicas_per_node: 1
    resources:
      limits:
        cpus: "1"
        memory: 1gb
             
 rabbitmq:
  image: rabbitmq:3.8-management-alpine
  hostname: "{{.Node.Hostname}}"
  environment:
    TZ: GMT
    RABBITMQ_DEFAULT_USER: ntbes
    RABBITMQ_DEFAULT_PASS: TkHNQ9GPVE8wbBKYNTsFx0JKM80ZrCoX
    RABBITMQ_NODENAME: ntbes
    RABBITMQ_VM_MEMORY_HIGH_WATERMARK: 25%
  volumes:
    - 'rabbitmq-data:/var/lib/rabbitmq'
  networks:
    ntbes:
      aliases:
        - mq.ntbes.local
  deploy:
    mode: replicated
    replicas: 1
    restart_policy:
      condition: on-failure
    placement:
      constraints:
        - node.role==manager
        
volumes:
  rabbitmq-data:
        
networks:
  ntbes:
EOF
```

{% hint style="info" %}
You can change environment variables according to your environment.
{% endhint %}

#### 2.5.3 Docker stack deploy

```bash
 docker stack deploy --with-registry-auth --compose-file dev.yml ntbes
```

## 3. Redeploy Application

#### 3.1 Collect applications tar files  from zone24x7 & load image

```bash
sudo docker load -i ntbes_admin-portal-ui.tar 
sudo docker load -i ntbes_email_sending_worker.tar 
sudo docker load -i ntbes_data_processing_worker.tar 
sudo docker load -i ntbes_file_generation_worker.tar 
```

#### 3.2 Update service images

```bash
docker service update --with-registry-auth --image nexus.zone24x7.lk/ntbes/ntbes_email_sending_worker:1.0.0-20210702.061228-33 ntbes_ntbes_email_sending_worker

docker service update --with-registry-auth --image <image:tag> <service name>
```

## 4 Useful Commands

List Services

```bash
docker service ls
ID             NAME                                 MODE         REPLICAS               IMAGE                                                                           PORTS
f67iido971rs   ntbes_ntbes-statement-generator      replicated   1/1 (max 1 per node)   nexus.zone24x7.lk/ntbes/ntbes-statement-generator:1.0.0-snapshot-202106041231   *:8081->8081/tcp
pcgqjfrgph60   ntbes_ntbes_data_processing_worker   replicated   1/1 (max 1 per node)   nexus.zone24x7.lk/ntbes/ntbes_data_processing_worker:1.0.0-20210628.095558-28   *:8086->8086/tcp
jaitlzishm1h   ntbes_ntbes_email_sending_worker     replicated   1/1 (max 1 per node)   nexus.zone24x7.lk/ntbes/ntbes_email_sending_worker:1.0.0-20210702.061228-33     *:8083->8083/tcp
svwcapbqhxq4   ntbes_ntbes_file_generation_worker   replicated   1/1 (max 1 per node)   nexus.zone24x7.lk/ntbes/ntbes_file_generation_worker:1.0.0-20210628.095650-28   *:8085->8085/tcp
8rsaudmtpigp   ntbes_rabbitmq                       replicated   1/1                    rabbitmq:3.8-management-alpine                                



```

List Docker Containers

```bash
docker ps
CONTAINER ID   IMAGE                                                                           COMMAND                  CREATED          STATUS          PORTS                                                                  NAMES
6a681eafd28c   nexus.zone24x7.lk/ntbes/ntbes_data_processing_worker:1.0.0-20210628.095558-28   "java -jar ntbes_dat…"   15 seconds ago   Up 9 seconds    8086/tcp                                                               ntbes_ntbes_data_processing_worker.1.qicfpf5oz8z3e0tb50oyp3os8
cbeeb661d06f   nexus.zone24x7.lk/ntbes/ntbes_file_generation_worker:1.0.0-20210628.095650-28   "java -jar ntbes_fil…"   22 seconds ago   Up 15 seconds   8085/tcp                                                               ntbes_ntbes_file_generation_worker.1.zodludxmv1y4e1ri1lsa9qpwh
fa9a7d44ca6c   rabbitmq:3.8-management-alpine                                                  "docker-entrypoint.s…"   2 days ago       Up 2 days       4369/tcp, 5671-5672/tcp, 15671-15672/tcp, 15691-15692/tcp, 25672/tcp   ntbes_rabbitmq.1.yt4la3faw2l2m622oricxfnw7
c22bd10ff454   nexus.zone24x7.lk/ntbes/ntbes-statement-generator:1.0.0-snapshot-202106041231   "java -jar statement…"   2 days ago       Up 2 days       8081/tcp                                                               ntbes_ntbes-statement-generator.1.sdb0lzt77nt2xwutmwf3iw3p9
0302758ebfce   nexus.zone24x7.lk/ntbes/ntbes_email_sending_worker:1.0.0-20210702.061228-33     "java -jar -Dserver.…"   2 days ago       Up 2 days       8083/tcp   

#list continuously
watch -d docker ps
```

Get service logs

```bash
docker service logs ntbes_ntbes-statement-generator
docker service logs -f ntbes_ntbes-statement-generator

docker service logs -f <service name>
docker service logs  <service name>

```



##  

