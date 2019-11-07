# Types of Load Balancing

###  <a id="types-of-load-balancing"></a>

Now that we have an understanding of the basic components that are used in load balancing, let's get into the basic types of load balancing.

#### No Load Balancing <a id="no-load-balancing"></a>

A simple web application environment with no load balancing might look like the following:

![No Load Balancing](https://assets.digitalocean.com/articles/HAProxy/web_server.png)

In this example, the user connects directly to your web server, at _yourdomain.com_ and there is no load balancing. If your single web server goes down, the user will no longer be able to access your web server. Additionally, if many users are trying to access your server simultaneously and it is unable to handle the load, they may have a slow experience or they may not be able to connect at all.

#### Layer 4 Load Balancing <a id="layer-4-load-balancing"></a>

The simplest way to load balance network traffic to multiple servers is to use layer 4 \(transport layer\) load balancing. Load balancing this way will forward user traffic based on IP range and port \(i.e. if a request comes in for [_http://yourdomain.com/anything_](http://yourdomain.com/anything), the traffic will be forwarded to the backend that handles all the requests for _yourdomain.com_ on _port 80_\). For more details on layer 4, check out the _TCP_ subsection of our [Introduction to Networking](https://www.digitalocean.com/community/tutorials/an-introduction-to-networking-terminology-interfaces-and-protocols#protocols).

Here is a diagram of a simple example of layer 4 load balancing:

![Layer 4 Load Balancing](https://assets.digitalocean.com/articles/HAProxy/layer_4_load_balancing.png)

The user accesses the load balancer, which forwards the user's request to the _web-backend_ group of backend servers. Whichever backend server is selected will respond directly to the user's request. Generally, all of the servers in the _web-backend_ should be serving identical content--otherwise the user might receive inconsistent content. Note that both web servers connect to the same database server.

#### Layer 7 Load Balancing <a id="layer-7-load-balancing"></a>

Another, more complex way to load balance network traffic is to use layer 7 \(application layer\) load balancing. Using layer 7 allows the load balancer to forward requests to different backend servers based on the content of the user's request. This mode of load balancing allows you to run multiple web application servers under the same domain and port. For more details on layer 7, check out the _HTTP_ subsection of our [Introduction to Networking](https://www.digitalocean.com/community/tutorials/an-introduction-to-networking-terminology-interfaces-and-protocols#protocols).

Here is a diagram of a simple example of layer 7 load balancing:

![Layer 7 Load Balancing](https://assets.digitalocean.com/articles/HAProxy/layer_7_load_balancing.png)

In this example, if a user requests _yourdomain.com/blog_, they are forwarded to the _blog_ backend, which is a set of servers that run a blog application. Other requests are forwarded to _web-backend_, which might be running another application. Both backends use the same database server, in this example.

A snippet of the example frontend configuration would look like this:

```text
frontend http  bind *:80  mode http  acl url_blog path_beg /blog  use_backend blog-backend if url_blog  default_backend web-backend
```

This configures a frontend named _http_, which handles all incoming traffic on port 80.

`acl url_blog path_beg /blog` matches a request if the path of the user's request begins with _/blog_.

`use_backend blog-backend if url_blog` uses the ACL to proxy the traffic to _blog-backend_.

`default_backend web-backend` specifies that all other traffic will be forwarded to _web-backend_.

