# Example Jenkinsfile

```java
pipeline { agent any    stages {        stage('FrontEnd Build') {            steps {                nodejs('NodeJS 8.11.2') {                    sh label: '', script: '''cd mlsi-frontend/                    npm install                    npm run build                    cd ..'''                }            }        }        stage('Maven build') {            steps {               sh label: '', script: '/usr/local/maven/bin/mvn clean package'            }            }        stage('Sonarqube') {            environment {                scannerHome = tool 'Sonar_Runner'            }            steps {                nodejs('NodeJS 8.11.2') {                    withSonarQubeEnv('SonarQube') {                        sh "${scannerHome}/bin/sonar-scanner   "                    }                    timeout(time: 10, unit: 'MINUTES') {                        waitForQualityGate abortPipeline: true                    }                }                }        }    }}
```

```bash
stage('Sonarqube') {    environment {        scannerHome = tool 'SonarQubeScanner'    }    steps {            withSonarQubeEnv('sonarqube') {                sh "${scannerHome}/bin/sonar-scanner"            }             timeout(time: 10, unit: 'MINUTES') {                         waitForQualityGate abortPipeline: true            }        }    }
```

