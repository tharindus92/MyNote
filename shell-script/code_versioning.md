# Code Versioning

## POM Versioning

```text
Version=`echo -e 'setns x=http://maven.apache.org/POM/4.0.0\ncat /x:project/x:version/text()' | xmllint --shell pom.xml | grep -v /`
```

## Node versioning

```bash
VERSIONL=`node -p "require('./version.json').version"`size=${#VERSIONL}VERSION="${VERSIONL:0:size-9}"echo $VERSION > temp.fileV_MAJOR=$(cut -d '.' -f 1 temp.file)V_MINOR=$(cut -d '.' -f 2 temp.file)V_PATCH=$(cut -d '.' -f 3 temp.file)    echo $V_PATCH > temp.fileV_PATCH=$(cut -d '-' -f 1 temp.file)    OLD_VERSION=$V_MAJOR.$V_MINOR.$V_PATCHV_PATCH=$((V_PATCH + 1))        newVERSION="$V_MAJOR.$V_MINOR.$V_PATCH"rm -rf temp.file     /usr/bin/sed -i 's/'"$VERSION"'/'"$newVERSION"'/g' version.json
```

## SBT Versioning

```bash
versionStrOriginal=$(cat version.sbt)VERSION=${versionStrOriginal:25}VERSION=${VERSION::-10}A=$(echo $versionStrOriginal| perl -ne 'chomp; print join(".", splice(@{[split/\./,$_]}, 0, -1), map {++$_} pop @{[split/\./,$_]}), "\n";')
```



