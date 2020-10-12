# faume-integration-app

```text
docker service create \
        --name faume-integration-app \
        --publish 8082:8082 \
        nexus.zone24x7.lk/faume/faume-integration-app:v-0.0.1
```

ENTRYPOINT \["java","-jar","/app/eva-store.jar","--spring.config.location=/app/application.properties"\]



```text
docker service create \
        --name faume-we-app \
        --publish 8081:8081 \
        nexus.zone24x7.lk/faume/faume-web-app:v-0.0.1

```

```text
docker swarm leave --force
docker swarm init --default-addr-pool 10.20.0.0/16
docker swarm init --default-addr-pool 172.81.0.0/16 --default-addr-pool-mask-length 24
docker network inspect ingress

```

[  
](
https://sandbox.faceauthme.com/web-app/v1/request-info
)[https://sandbox.faceauthme.com](https://sandbox.faceauthme.com)

 [https://sandbox-integration.faceauthme.com](https://sandbox-integration.faceauthme.com) 

[https://sandbox-integration.faceauthme.com/app/info](https://sandbox-integration.faceauthme.com/app/info)

 [http://10.101.15.55:8081/app/info](http://10.101.15.55:8081/app/info) 

[http://10.101.15.55:8082/app/info](http://10.101.15.55:8082/app/info)

 [https://sandbox.faceauthme.com/](https://sandbox.faceauthme.com/)

 [https://sandbox.faceauthme.com/web-app](https://sandbox.faceauthme.com/web-app)

 [https://sandbox.faceauthme.com/web-app/v1/request-info](https://sandbox.faceauthme.com/web-app/v1/request-info)[  
](
https://sandbox.faceauthme.com/web-app/v1/request-info
)

