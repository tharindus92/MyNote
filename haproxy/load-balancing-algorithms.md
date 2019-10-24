# Load Balancing Algorithms

The load balancing algorithm that is used determines which server, in a backend, will be selected when load balancing. HAProxy offers several options for algorithms. In addition to the load balancing algorithm, servers can be assigned a _weight_ parameter to manipulate how frequently the server is selected, compared to other servers.

Because HAProxy provides so many load balancing algorithms, we will only describe a few of them here. See the [HAProxy Configuration Manual](http://cbonte.github.io/haproxy-dconv/configuration-1.4.html#4.2-balance) for a complete list of algorithms.

A few of the commonly used algorithms are as follows:

#### roundrobin <a id="roundrobin"></a>

Round Robin selects servers in turns. This is the default algorithm.

#### leastconn <a id="leastconn"></a>

Selects the server with the least number of connections--it is recommended for longer sessions. Servers in the same backend are also rotated in a round-robin fashion.

#### source <a id="source"></a>

This selects which server to use based on a hash of the source IP i.e. your user's IP address. This is one method to ensure that a user will connect to the same server.

### Sticky Sessions <a id="sticky-sessions"></a>

Some applications require that a user continues to connect to the same backend server. This persistence is achieved through sticky sessions, using the _appsession_ parameter in the backend that requires it.

### Health Check <a id="health-check"></a>

HAProxy uses health checks to determine if a backend server is available to process requests. This avoids having to manually remove a server from the backend if it becomes unavailable. The default health check is to try to establish a TCP connection to the server i.e. it checks if the backend server is listening on the configured IP address and port.

If a server fails a health check, and therefore is unable to serve requests, it is automatically disabled in the backend i.e. traffic will not be forwarded to it until it becomes healthy again. If all servers in a backend fail, the service will become unavailable until at least one of those backend servers becomes healthy again.

For certain types of backends, like database servers in certain situations, the default health check is insufficient to determine whether a server is still healthy.

