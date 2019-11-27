# when condition

```groovy
stage('Master Develop') {
        when {
            expression { BRANCH_NAME ==~ /(master|develop)/ }
        }
        steps {
                echo  env.BRANCH_NAME                  

        }
}


when { branch "feature/**/develop" }


when {
    not {
        anyOf {
            branch 'master';
            branch 'develop';
            branch "feature/**/develop"
        }
    }

}
```

