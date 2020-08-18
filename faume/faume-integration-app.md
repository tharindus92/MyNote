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

