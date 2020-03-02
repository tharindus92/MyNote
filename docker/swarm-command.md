# SWARM COMMAND

```text
docker swarm init --advertise-addr <MANAGER-IP>
docker node ls
docker swarm join-token worker
docker swarm join \
  --token  SWMTKN-1-49nj1cmql0jkz5s954yi3oex3nedyz0fb0xx14ie39trti4wxv-8vxv8rssmk743ojnwacrr2e7c \
  192.168.99.100:2377
  docker service ls
  
docker service ls

docker service inspect --pretty helloworld

docker service ps helloworld

docker service scale helloworld=5

docker service rm helloworld


```

