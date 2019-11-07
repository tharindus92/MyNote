# Introduction

HAProxy, which stands for High Availability Proxy, is a popular open source software TCP/HTTP Load Balancer and proxying solution which can be run on Linux, Solaris, and FreeBSD. Its most common use is to improve the performance and reliability of a server environment by distributing the workload across multiple servers \(e.g. web, application, database\). It is used in many high-profile environments, including: GitHub, Imgur, Instagram, and Twitter.

### HAProxy Terminology <a id="haproxy-terminology"></a>

There are many terms and concepts that are important when discussing load balancing and proxying. We will go over commonly used terms in the following sub-sections.

Before we get into the basic types of load balancing, we will talk about ACLs, backends, and frontends.

#### Access Control List \(ACL\) <a id="access-control-list-acl"></a>

In relation to load balancing, ACLs are used to test some condition and perform an action \(e.g. select a server, or block a request\) based on the test result. Use of ACLs allows flexible network traffic forwarding based on a variety of factors like pattern-matching and the number of connections to a backend, for example.

Example of an ACL:

```text
acl url_blog path_beg /blog
```

This ACL is matched if the path of a user's request begins with _/blog_. This would match a request of [_http://yourdomain.com/blog/blog-entry-1_](http://yourdomain.com/blog/blog-entry-1), for example.

For a detailed guide on ACL usage, check out the [HAProxy Configuration Manual](http://cbonte.github.io/haproxy-dconv/configuration-1.4.html#7).

#### Backend <a id="backend"></a>

A backend is a set of servers that receives forwarded requests. Backends are defined in the _backend_ section of the HAProxy configuration. In its most basic form, a backend can be defined by:

* which load balance algorithm to use
* a list of servers and ports

A backend can contain one or many servers in it--generally speaking, adding more servers to your backend will increase your potential load capacity by spreading the load over multiple servers. Increase reliability is also achieved through this manner, in case some of your backend servers become unavailable.

Here is an example of a two backend configuration, _web-backend_ and _blog-backend_ with two web servers in each, listening on port 80:

```text
backend web-backend   balance roundrobin   server web1 web1.yourdomain.com:80 check   server web2 web2.yourdomain.com:80 checkbackend blog-backend   balance roundrobin   mode http   server blog1 blog1.yourdomain.com:80 check   server blog1 blog1.yourdomain.com:80 check
```

`balance roundrobin` line specifies the load balancing algorithm, which is detailed in the [Load Balancing Algorithms](https://www.digitalocean.com/community/tutorials/an-introduction-to-haproxy-and-load-balancing-concepts#load-balancing-algorithms) section.

`mode http` specifies that layer 7 proxying will be used, which is explained in [Types of Load Balancing](https://www.digitalocean.com/community/tutorials/an-introduction-to-haproxy-and-load-balancing-concepts#types-of-load-balancing) section.

The `check` option at the end of the `server` directives specifies that health checks should be performed on those backend servers.

#### Frontend <a id="frontend"></a>

A frontend defines how requests should be forwarded to backends. Frontends are defined in the _frontend_ section of the HAProxy configuration. Their definitions are composed of the following components:

* a set of IP addresses and a port \(e.g. 10.1.1.7:80, \*:443, etc.\)
* ACLs
* _use\_backend_ rules, which define which backends to use depending on which ACL conditions are matched, and/or a _default\_backend_ rule that handles every other case

A frontend can be configured to various types of network traffic, as explained in the next section.

The syntax is:

```bash
frontend <frontend name>    bind <IPs or wild card>:80    acl <aclname> <criterion> [flags] [operator] [<value>] ...    use_backend <backend name> if <aclname>
```

Here's an example with all of the details filled in:

```text
frontend myAppFrontEnd  bind *:80  acl acl_myApp path_sub myApp  use_backend myAppBackend if acl_myApp
```

* **`myAppFrontEnd`**: Name of the frontend.

The frontend listens on _port 80_ to all the interfaces `*`  available in server. If the URL has _myApp_ as a substring, the request is forwarded to `myAppBackend`. Otherwise, you will get no service available exception.





### The Format <a id="the-format"></a>

If you’re using HAProxy Enterprise to get ahold of its advanced features, the configuration file can be found at **/etc/hapee-1.8/hapee-lb.cfg**. If you’re using the Community Edition, it’s at **/etc/haproxy/haproxy.cfg**. 

```text
global    # global settings heredefaults    # defaults herefrontend    # a frontend that accepts requests from clientsbackend    # servers that fulfill the requests
```



### What About Listen? <a id="what-about-listen"></a>

As you’ve seen, `frontend` and `backend` sections receive traffic and send it to a pool of servers. You can also use `listen` sections to do the same thing. They, essentially, combine the functionality of a `frontend` and `backend` into one. You may prefer the readability gained by having separate `frontend` and `backend` sections, especially when using HTTP mode with its many options, or you may favor a more concise configuration, making `listen` the preferred approach. In either case, you have the full power of HAProxy at your fingertips!

Here’s a simple example of a `listen` used to serve up the [HAProxy Stats page](https://www.haproxy.com/blog/exploring-the-haproxy-stats-page/):  


```text
listen stats    bind *:8404    stats enable    stats uri /monitor    stats refresh 5s
```

### Statistics \(Optional\)

Add the optional stats node to the configuration:/etc/haproxy/haproxy.cfg

```text
listen stats    bind :32700    stats enable    stats uri /    stats hide-version    stats auth someuser:password
```

The HAProxy stats node will listen on port 32700 for connections and is configured to hide the version of HAProxy as well as to require a password login. Replace `password` with a more secure password. In addition, it is recommended to disable stats login in production.

