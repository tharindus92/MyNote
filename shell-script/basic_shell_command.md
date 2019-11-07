# Basic Shell Command

## If statement

```bash
sudo systemctl $1 $elementif [ $? -eq 0 ]; then       echo -e "\e[90m\e[1m\t Successfuly $1 $element services \e[0m"else      echo -e "\e[91m\e[1m\t $1 services  failed \n\e[0m"       exit 1;          fi
```

### Directory Existense

```bash
if [ -d "$file" ]then    mv $backupfile.tgz  $BackupDir fi
```

## For loop

```bash
for element in "${SERVICE_LIST[@]}"do    ………………………………….    …………………………………done
```

### Jenkins Server Iteration

```bash
for SERVER in $(echo $Server_Selection |sed -n 1'p' | tr ',' '\n')do    ssh $user@$SERVER bash -s < scripts/Deployment.sh $version prod done
```

## Array

```bash
SERVICE_LIST=(               "auth_api_impl"                "event_api_impl"                 "notification_api_impl"                 "sensorReading_impl"                 "workflow_impl"                "workflow_scheduler_impl"                 )for element in "${SERVICE_LIST[@]}"do       sudo systemctl start $elementdone
```

## Switch Statement

```bash
action=$1case "$action" inStatus)            ./bp status ;; Stop)                echo -e "\n\e[32m\e[1m Stop Application \e[0m";;Start)./bp start;;Restart)           ./bp restart;;esac
```

## RM Files more than N

```bash
ls -t | tail -n +4 | xargs rm –-ormax=3n=0ls -1t *.tgz |while read file; don=$((n+1))if [[ $n -gt $max ]]; thenrm -f "$file"fidone
```

## String Comparison 

```bash
if [ "$str1" == "$str2" ]then   	repository="snapshots"   elif [ "$str1" == "$str3" ]then  	repository="releases"   fi
```

## Curl

```bash
curl -m 30 -s -X GET "http://$SERVER:$PORT/v1/das/propensity?id=0" -i | head -5curl -m 30 -s -X GET "http://$SERVER:$PORT/v2/das/propensity?atgId=0" -i | grep 'HTTP/1.1 200 OK' > /dev/null
```

### curl nexus push

```text
curl -v -u $username:$password --upload-file $WORKSPACE/CMON/cmon-1.0.0.tar.gz $nexusurl/$repository/$group/$artifactId/$version/$zipfile
```

## Sed

```bash
sed -i s/localhost:3334/$BPIP:$BPPORT/ $CONF_LOC/application.confsed -i s/PORT=8080/PORT=7080/ $CONF_LOC/$ExecutableFilesed -i "s/^\(.*\)\(versionCode \)\(.*\)$/$(printf '\t')versionCode ${VERSION_CODE_NEW}/g" build.gradle
```

## Date

```bash
mv $APP_NAME.tgz $APP_NAME-$(date +%F-%H:%M).tgz
```

## GIT

```bash
git tag -a $B -m "version $VERSION"git push origin --tagsgit add .git commit -am "Updated version number"git push origin HEAD:master
```

