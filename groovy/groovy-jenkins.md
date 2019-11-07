# Groovy jenkins

## Nexus List

```groovy
import groovy.xml.*;import groovy.util.*;myUrl = "https://nexus.zone24x7.lk/repository/snapshots/com/zone/xfil/finance_ap/maven-metadata.xml"def data = new URL(myUrl).getText()def dataObj = new XmlParser().parseText(data)def versions=[]for (v in dataObj.versioning[0].versions[0].version){          versions.add(v.value()[0])}versions.sort(false).reverse()
```

## Download Nexus Snapshot

```bash
NEXUS_URL=https://nexus.zone24x7.lkMAVEN_REPO=snapshotsGROUP_ID=com.zone.xfilARTIFACT_ID=finance_apVERSION=1.0.0-SNAPSHOTFILE_EXTENSION=tar.gzdownload_url=$(curl -X GET "${NEXUS_URL}/service/rest/v1/search/assets?repository=${MAVEN_REPO}&maven.groupId=${GROUP_ID}&maven.artifactId=${ARTIFACT_ID}&maven.baseVersion=${VERSION}&maven.extension=${FILE_EXTENSION}" -H  "accept: application/json"  | jq -rc '.items | .[].downloadUrl' | sort | tail -n 1)wget $download_url
```

