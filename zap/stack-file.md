# Stack file

```text
version: "3.8"
services:
  zap:
    image: "owasp/zap2docker-bare"
    environment:
      ZAP_PORT: 8090
    ports:
      - "8090:8090"
    command: zap.sh -daemon -port 8090 -host 0.0.0.0 -config 'api.key=12345' -config 'api.addrs.addr.name=.*' -config 'api.addrs.addr.regex=true' -addonupdate -addonlist -addoninstall pscanrulesAlpha -addoninstall pscanrulesBeta
```

