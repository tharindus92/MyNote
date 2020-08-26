# Maven command to nexus

```bash
#!/bin/bash

NexusUrl="https://nexus.zone24x7.lk/repository/releases"
GroupID="com.zone.faume"
ArtifactID="openvino_toolkit"
format="tgz"
NEXUS_ID=nexus
NEXUS_CONFIG=/jenkins/nexus/mavenSettings.xml
VERSION=1.0.0

mvn -q deploy:deploy-file -DgroupId=$GroupID -DartifactId=$ArtifactID -Dversion=$VERSION -DgeneratePom=true -Dpackaging=$format -DrepositoryId=$NEXUS_ID -Durl=$NexusUrl  -Dfile="l_openvino_toolkit_p_2020.3.194.tgz"  -s $NEXUS_CONFIG ;


```

