# Untitled

{% embed url="https://jenkins-linux.zone24x7.lk/job/BXGRN/job/HYDRA/job/OLD\_BUILD/job/ZAP\_SCAN" %}

```text

******************************************** headless api ************************************************************************************************************************************

docker run -u zap -p 8080:8080 -i owasp/zap2docker-stable zap.sh -daemon -host 0.0.0.0 -port 8080 -config api.addrs.addr.name=.* -config api.addrs.addr.regex=true -config api.disablekey=true 

***
docker run -p 8090:8090 -i owasp/zap2docker-stable zap.sh -daemon -port 8090 -host 0.0.0.0 -config api.disablekey=true -config 'api.addrs.addr.name=.*' -config api.addrs.addr.regex=true -addonlist -addoninstall pscanrulesAlpha -addoninstall pscanrulesBeta


***********************************************************************************************************************************************************************************************


************************************* Web switch **********************************************************************************************************************************************

docker run -u zap -p 8080:8080 -p 8090:8090 -i owasp/zap2docker-stable zap-webswing.sh

docker run -v $(pwd):/zap/wrk/:rw -e ZAP_WEBSWING_OPTS="-host 0.0.0.0 -port 9090" -u zap -p 8080:8080 -p 9090:9090 -i owasp/zap2docker-stable zap-webswing.sh

************************************************************************************************************************************************************************************************


./zap.sh -daemon -quickurl https://ezp-stage.ezpassport.online/  -quickout ./result2.html


docker run -p 8090:8090 -i owasp/zap2docker-stable zap.sh -daemon -port 8090 -host 0.0.0.0 -config 'api.key=12345' -config 'api.addrs.addr.name=.*' -config api.addrs.addr.regex=true -addonlist -addoninstall pscanrulesAlpha -addoninstall pscanrulesBeta

zap-cli --api-key "12345" --zap-url 10.101.15.100  quick-scan -o '-config api.key=12345' -s xss,sqli  --spider -r  https://ezp-stage.ezpassport.online && zap-cli --api-key "12345" --zap-url 10.101.15.100  report -o abc.html -f html
```

{% embed url="https://medium.com/cloudadventure/security-in-a-ci-cd-pipeline-876ed8541fa4" %}



