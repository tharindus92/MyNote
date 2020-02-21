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



