# Swarm



### Docker Engine 1.12 or newer[🔗](https://docs.docker.com/engine/swarm/swarm-tutorial/#open-protocols-and-ports-between-the-hosts#docker-engine-112-or-newer)

This tutorial requires Docker Engine 1.12 or newer on each of the host machines. Install Docker Engine and verify that the Docker Engine daemon is running on each of the machines. You can get the latest version of Docker Engine as follows:

* [install Docker Engine on Linux machines](https://docs.docker.com/engine/swarm/swarm-tutorial/#install-docker-engine-on-linux-machines)
* [use Docker Desktop for Mac or Docker Desktop for Windows](https://docs.docker.com/engine/swarm/swarm-tutorial/#use-docker-for-mac-or-docker-for-windows)

### Open protocols and ports between the hosts

The following ports must be available. On some systems, these ports are open by default.

* **TCP port 2377** for cluster management communications
* **TCP** and **UDP port 7946** for communication among nodes
* **UDP port 4789** for overlay network traffic

If you plan on creating an overlay network with encryption \(`--opt encrypted`\), you also need to ensure **ip protocol 50** \(**ESP**\) traffic is allowed.

## Create a swarm

After you complete the [tutorial setup](https://docs.docker.com/engine/swarm/swarm-tutorial/) steps, you’re ready to create a swarm. Make sure the Docker Engine daemon is started on the host machines.

1. Open a terminal and ssh into the machine where you want to run your manager node. This tutorial uses a machine named `manager1`. If you use Docker Machine, you can connect to it via SSH using the following command:

   ```text
   $ docker-machine ssh manager1
   ```

2. Run the following command to create a new swarm:

   ```text
   $ docker swarm init --advertise-addr <MANAGER-IP>
   ```

   > **Note**: If you are using Docker Desktop for Mac or Docker Desktop for Windows to test single-node swarm, simply run `docker swarm init` with no arguments. There is no need to specify `--advertise-addr` in this case. To learn more, see the topic on how to [Use Docker Desktop or Mac or Docker Desktop for Windows](https://docs.docker.com/engine/swarm/swarm-tutorial/#use-docker-for-mac-or-docker-for-windows) with Swarm.

   In the tutorial, the following command creates a swarm on the `manager1` machine:

   ```text
   $ docker swarm init --advertise-addr 192.168.99.100
   Swarm initialized: current node (dxn1zf6l61qsb1josjja83ngz) is now a manager.

   To add a worker to this swarm, run the following command:

       docker swarm join \
       --token SWMTKN-1-49nj1cmql0jkz5s954yi3oex3nedyz0fb0xx14ie39trti4wxv-8vxv8rssmk743ojnwacrr2e7c \
       192.168.99.100:2377

   To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
   ```

   The `--advertise-addr` flag configures the manager node to publish its address as `192.168.99.100`. The other nodes in the swarm must be able to access the manager at the IP address.

   The output includes the commands to join new nodes to the swarm. Nodes will join as managers or workers depending on the value for the `--token` flag.

3. Run `docker info` to view the current state of the swarm:

   ```text
   $ docker info

   Containers: 2
   Running: 0
   Paused: 0
   Stopped: 2
     ...snip...
   Swarm: active
     NodeID: dxn1zf6l61qsb1josjja83ngz
     Is Manager: true
     Managers: 1
     Nodes: 1
     ...snip...
   ```

4. Run the `docker node ls` command to view information about nodes:

   ```text
   $ docker node ls

   ID                           HOSTNAME  STATUS  AVAILABILITY  MANAGER STATUS
   dxn1zf6l61qsb1josjja83ngz *  manager1  Ready   Active        Leader
   ```

The `*` next to the node ID indicates that you’re currently connected on this node.

Docker Engine swarm mode automatically names the node for the machine host name. The tutorial covers other columns in later steps.

## Add nodes to the swarm

Once you’ve [created a swarm](https://docs.docker.com/engine/swarm/swarm-tutorial/create-swarm/) with a manager node, you’re ready to add worker nodes.

1. Open a terminal and ssh into the machine where you want to run a worker node. This tutorial uses the name `worker1`.
2. Run the command produced by the `docker swarm init` output from the [Create a swarm](https://docs.docker.com/engine/swarm/swarm-tutorial/create-swarm/) tutorial step to create a worker node joined to the existing swarm:

   ```text
   $ docker swarm join \
     --token  SWMTKN-1-49nj1cmql0jkz5s954yi3oex3nedyz0fb0xx14ie39trti4wxv-8vxv8rssmk743ojnwacrr2e7c \
     192.168.99.100:2377

   This node joined a swarm as a worker.
   ```

   If you don’t have the command available, you can run the following command on a manager node to retrieve the join command for a worker:

   ```text
   $ docker swarm join-token worker

   To add a worker to this swarm, run the following command:

       docker swarm join \
       --token SWMTKN-1-49nj1cmql0jkz5s954yi3oex3nedyz0fb0xx14ie39trti4wxv-8vxv8rssmk743ojnwacrr2e7c \
       192.168.99.100:2377
   ```

3. Open a terminal and ssh into the machine where you want to run a second worker node. This tutorial uses the name `worker2`.
4. Run the command produced by the `docker swarm init` output from the [Create a swarm](https://docs.docker.com/engine/swarm/swarm-tutorial/create-swarm/) tutorial step to create a second worker node joined to the existing swarm:

   ```text
   $ docker swarm join \
     --token SWMTKN-1-49nj1cmql0jkz5s954yi3oex3nedyz0fb0xx14ie39trti4wxv-8vxv8rssmk743ojnwacrr2e7c \
     192.168.99.100:2377

   This node joined a swarm as a worker.
   ```

5. Open a terminal and ssh into the machine where the manager node runs and run the `docker node ls` command to see the worker nodes:

   ```text
   ID                           HOSTNAME  STATUS  AVAILABILITY  MANAGER STATUS
   03g1y59jwfg7cf99w4lt0f662    worker2   Ready   Active
   9j68exjopxe7wfl6yuxml7a7j    worker1   Ready   Active
   dxn1zf6l61qsb1josjja83ngz *  manager1  Ready   Active        Leader
   ```

   The `MANAGER` column identifies the manager nodes in the swarm. The empty status in this column for `worker1` and `worker2` identifies them as worker nodes.

   Swarm management commands like `docker node ls` only work on manager nodes.



## Deploy a service to the swarm

After you [create a swarm](https://docs.docker.com/engine/swarm/swarm-tutorial/create-swarm/), you can deploy a service to the swarm. For this tutorial, you also [added worker nodes](https://docs.docker.com/engine/swarm/swarm-tutorial/add-nodes/), but that is not a requirement to deploy a service.

1. Open a terminal and ssh into the machine where you run your manager node. For example, the tutorial uses a machine named `manager1`.
2. Run the following command:

   ```text
   $ docker service create --replicas 1 --name helloworld alpine ping docker.com

   9uk4639qpg7npwf3fn2aasksr
   ```

   * The `docker service create` command creates the service.
   * The `--name` flag names the service `helloworld`.
   * The `--replicas` flag specifies the desired state of 1 running instance.
   * The arguments `alpine ping docker.com` define the service as an Alpine Linux container that executes the command `ping docker.com`.

3. Run `docker service ls` to see the list of running services:

   ```text
   $ docker service ls

   ID            NAME        SCALE  IMAGE   COMMAND
   9uk4639qpg7n  helloworld  1/1    alpine  ping docker.com
   ```

## Inspect a service on the swarm <a id="title"></a>

Estimated reading time: 2 minutes

When you have [deployed a service](https://docs.docker.com/engine/swarm/swarm-tutorial/deploy-service/) to your swarm, you can use the Docker CLI to see details about the service running in the swarm.

1. If you haven’t already, open a terminal and ssh into the machine where you run your manager node. For example, the tutorial uses a machine named `manager1`.
2. Run `docker service inspect --pretty <SERVICE-ID>` to display the details about a service in an easily readable format.

   To see the details on the `helloworld` service:

   ```text
   [manager1]$ docker service inspect --pretty helloworld

   ID:		9uk4639qpg7npwf3fn2aasksr
   Name:		helloworld
   Service Mode:	REPLICATED
    Replicas:		1
   Placement:
   UpdateConfig:
    Parallelism:	1
   ContainerSpec:
    Image:		alpine
    Args:	ping docker.com
   Resources:
   Endpoint Mode:  vip
   ```

   > **Tip**: To return the service details in json format, run the same command without the `--pretty` flag.

   ```text
   [manager1]$ docker service inspect helloworld
   [
   {
       "ID": "9uk4639qpg7npwf3fn2aasksr",
       "Version": {
           "Index": 418
       },
       "CreatedAt": "2016-06-16T21:57:11.622222327Z",
       "UpdatedAt": "2016-06-16T21:57:11.622222327Z",
       "Spec": {
           "Name": "helloworld",
           "TaskTemplate": {
               "ContainerSpec": {
                   "Image": "alpine",
                   "Args": [
                       "ping",
                       "docker.com"
                   ]
               },
               "Resources": {
                   "Limits": {},
                   "Reservations": {}
               },
               "RestartPolicy": {
                   "Condition": "any",
                   "MaxAttempts": 0
               },
               "Placement": {}
           },
           "Mode": {
               "Replicated": {
                   "Replicas": 1
               }
           },
           "UpdateConfig": {
               "Parallelism": 1
           },
           "EndpointSpec": {
               "Mode": "vip"
           }
       },
       "Endpoint": {
           "Spec": {}
       }
   }
   ]
   ```

3. Run `docker service ps <SERVICE-ID>` to see which nodes are running the service:

   ```text
   [manager1]$ docker service ps helloworld

   NAME                                    IMAGE   NODE     DESIRED STATE  CURRENT STATE           ERROR               PORTS
   helloworld.1.8p1vev3fq5zm0mi8g0as41w35  alpine  worker2  Running        Running 3 minutes
   ```

   In this case, the one instance of the `helloworld` service is running on the `worker2` node. You may see the service running on your manager node. By default, manager nodes in a swarm can execute tasks just like worker nodes.

   Swarm also shows you the `DESIRED STATE` and `CURRENT STATE` of the service task so you can see if tasks are running according to the service definition.

4. Run `docker ps` on the node where the task is running to see details about the container for the task.

   > **Tip**: If `helloworld` is running on a node other than your manager node, you must ssh to that node.

   ```text
   [worker2]$docker ps

   CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
   e609dde94e47        alpine:latest       "ping docker.com"   3 minutes ago       Up 3 minutes                            hell
   ```





## Scale the service in the swarm <a id="title"></a>

Estimated reading time: 1 minute

Once you have [deployed a service](https://docs.docker.com/engine/swarm/swarm-tutorial/deploy-service/) to a swarm, you are ready to use the Docker CLI to scale the number of containers in the service. Containers running in a service are called “tasks.”

1. If you haven’t already, open a terminal and ssh into the machine where you run your manager node. For example, the tutorial uses a machine named `manager1`.
2. Run the following command to change the desired state of the service running in the swarm:

   ```text
   $ docker service scale <SERVICE-ID>=<NUMBER-OF-TASKS>
   ```

   For example:

   ```text
   $ docker service scale helloworld=5

   helloworld scaled to 5
   ```

3. Run `docker service ps <SERVICE-ID>` to see the updated task list:

   ```text
   $ docker service ps helloworld

   NAME                                    IMAGE   NODE      DESIRED STATE  CURRENT STATE
   helloworld.1.8p1vev3fq5zm0mi8g0as41w35  alpine  worker2   Running        Running 7 minutes
   helloworld.2.c7a7tcdq5s0uk3qr88mf8xco6  alpine  worker1   Running        Running 24 seconds
   helloworld.3.6crl09vdcalvtfehfh69ogfb1  alpine  worker1   Running        Running 24 seconds
   helloworld.4.auky6trawmdlcne8ad8phb0f1  alpine  manager1  Running        Running 24 seconds
   helloworld.5.ba19kca06l18zujfwxyc5lkyn  alpine  worker2   Running        Running 24 seconds
   ```

   You can see that swarm has created 4 new tasks to scale to a total of 5 running instances of Alpine Linux. The tasks are distributed between the three nodes of the swarm. One is running on `manager1`.

4. Run `docker ps` to see the containers running on the node where you’re connected. The following example shows the tasks running on `manager1`:

   ```text
   $ docker ps

   CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
   528d68040f95        alpine:latest       "ping docker.com"   About a minute ago   Up About a minute                       helloworld.4.auky6trawmdlcne8ad8phb0f1
   ```

   If you want to see the containers running on other nodes, ssh into those nodes and run the `docker ps` command.



## Delete the service running on the swarm <a id="title"></a>

Estimated reading time: 1 minute

The remaining steps in the tutorial don’t use the `helloworld` service, so now you can delete the service from the swarm.

1. If you haven’t already, open a terminal and ssh into the machine where you run your manager node. For example, the tutorial uses a machine named `manager1`.
2. Run `docker service rm helloworld` to remove the `helloworld` service.

   ```text
   $ docker service rm helloworld

   helloworld
   ```

3. Run `docker service inspect <SERVICE-ID>` to verify that the swarm manager removed the service. The CLI returns a message that the service is not found:

   ```text
   $ docker service inspect helloworld
   []
   Error: no such service: helloworld
   ```

4. Even though the service no longer exists, the task containers take a few seconds to clean up. You can use `docker ps` on the nodes to verify when the tasks have been removed.

   ```text
   $ docker ps

       CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
       db1651f50347        alpine:latest       "ping docker.com"        44 minutes ago      Up 46 seconds                           helloworld.5.9lkmos2beppihw95vdwxy1j3w
       43bf6e532a92        alpine:latest       "ping docker.com"        44 minutes ago      Up 46 seconds                           helloworld.3.a71i8rp6fua79ad43ycocl4t2
       5a0fb65d8fa7        alpine:latest       "ping docker.com"        44 minutes ago      Up 45 seconds                           helloworld.2.2jpgensh7d935qdc857pxulfr
       afb0ba67076f        alpine:latest       "ping docker.com"        44 minutes ago      Up 46 seconds                           helloworld.4.1c47o7tluz7drve4vkm2m5olx
       688172d3bfaa        alpine:latest       "ping docker.com"        45 minutes ago      Up About a minute                       helloworld.1.74nbhb3fhud8jfrhigd7s29we

   $ docker ps
      CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               
   ```



