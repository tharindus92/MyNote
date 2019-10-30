# Docker Commands

docker rm 4e4fa871ac89 -f

docker exec -it f1183cf3fbef /bin/sh

### Push docker image to nexus repository

Created by [Abhinay \(Unlicensed\)](https://publichealthsurveillance.atlassian.net/wiki/people/5a790ec1eec45c782260e26f?ref=confluence&src=profilecard)Last updated [Jul 25, 2018](https://publichealthsurveillance.atlassian.net/wiki/pages/diffpagesbyversion.action?pageId=353697805&selectedPageVersions=5&selectedPageVersions=6)

**Below are the steps to build and publish docker images to nexus repository.**

a. Login to nexus server with credentials

```text
docker login -u username imagehub.cdc.gov:5989
```

b. create dockerfile with required configuration

c. build docker image locally using Dockerfile with tag which point to nexus repository.

```text
docker build -t imagehub.cdc.gov:5989/helloworld:latest .
```

d. Then push the image to nexus repository

```text
docker push imagehub.cdc.gov:5989/helloworld:latest
```

