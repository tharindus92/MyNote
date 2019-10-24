# Maven command to nexus

```bash
mvn -q deploy:deploy-file 
-DgroupId=$GroupID 
-DartifactId=$ArtifactID 
-Dversion=$VERSION 
-DgeneratePom=true 
-Dpackaging=$FORMAT 
-DrepositoryId=$NEXUS_ID 
-Durl=$NexusUrl  
-Dfile=$FILE  
-s $NEXUS_CONFIG

```

