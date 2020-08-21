# Key Generate

```text
openssl genrsa -out "root-ca.key" 4096

openssl req \
          -new -key "root-ca.key" \
          -out "root-ca.csr" -sha256 \
          -subj '/C=LK/ST=Western/L=Colombo/O=Zone24x7/CN=frontend faume'
          


openssl req -new -key "site.key" -out "site.csr" -sha256 \
          -subj '/C=LK/ST=Western/L=Colombo/O=Zone24x7/CN=localhost'
```

{% embed url="https://docs.docker.com/engine/swarm/secrets/" %}



```text
--secret src=homepage,target="\inetpub\wwwroot\index.html"
```

