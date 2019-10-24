# Using OWASP Dependency Check as Jenkins plugin

**Checking vulnerabilities in 3rd party dependencies using OWASP Dependency-Check Plugin in Jenkins**

Vulnerabilities are Everywhere. When creating secure software, caring about the security of our own code is not only the important factor. Vulnerabilities might also arise from a 3rd party component, so handling those vulnerabilities is another important aspect of creating secure software.

**OWASP Dependency Check**

OWASP Dependency-Check is an open source solution of the OWASP which identifies project dependencies and checks if there are any known, publicly disclosed, vulnerabilities.Dependency-Check can currently be used to scan Java, .NET, Python, Ruby \(gemspec\), PHP \(composer\), and Node.js applications \(and their dependent libraries\), additionally, it provides limited support for C/C++ projects\[1\]\[2\]

Dependency-Check includes a command line interface \(CLI\), an Ant task, and Maven plugin. All three generate the same HTML and XML reports. The Dependency-Check Jenkins Plugin relies on the XML report generated from the CLI, Ant task or Maven plugin\[1\]

**Configuring OWASP Dependency-Check Plugin in Jenkins to check vulnerabilities**

1. Install OWASP Dependency-Check Plugin\[3\] Manage jenkins → Manage plugins → Available

![](https://cdn-images-1.medium.com/max/800/1*DFrfQkuS1iK1A0OUcFkrLQ.png)

1. Now create a new job using new Item option and click OK\(I have created the project as a Freestyle project\)![](https://cdn-images-1.medium.com/max/800/1*TI5Zg03tt1F943WGwE9wwA.png)
2. Then you will be redirected to the job configuration section, Configure the source code management section with git/subversion URL![](https://cdn-images-1.medium.com/max/800/1*egcyQFK_ve-NSuhxSj2q-w.png)
3. Configure an external job to maintain NVD updates

When ever we run the build steps of OWASP dependency-check\(**Invoke OWASP Dependancy-Check analysis** option\) with the default configurations, it will check each and every dependencies in our job with National Vulnerability Database, so it is always recommended to maintain a separate job which can snapshot and maintain NVD in our local machine.

**To do that -&gt;**

* Create another freestyle job
* Configure a build trigger which can run your job periodically, like daily

![](https://cdn-images-1.medium.com/max/800/1*evhGSYa_80kqwcq-tAGjzg.png)

* Configure the build option to get the NVD updates, and specify the path where the updates needs to be stored

![](https://cdn-images-1.medium.com/max/800/1*JwJTbn3F2Q0Sg6sWHiDCqw.png)

* Now build the new job.

After the build NVD snapshot will be available in the Data Directory we have configured.

1. Configure Dependency-Check-Plugin for the job

Now in your job **Build** option, select **Invoke OWASP Dependancy-Check analysis** as the build step.

Click on the **Advanced** button and enter the data directory**\(Local NVD File\)**configured in the external job before. Activate the **Disable NVD auto-update**checkbox since all updates are done by the external job.![](https://cdn-images-1.medium.com/max/800/1*ZcM9AFlvSXt0Tmi9KFRtbg.png)

**Note** -: If you don’t like to have an additional job configuration you can skip step 4 and leave the option Disable NVD auto-update disabled, but when doing the analysis using the global NVD in every build is time-consuming.

1. Publish analysis results and configure the status thresholds.

Add **Publish OWASP Dependency-Check analysis results** as a post build action and configure the status thresholds as you like![](https://cdn-images-1.medium.com/max/800/1*o_U_sLje6UkcqUJr1VvY3Q.png)

Save and run your job. Like many security scan results, these report you get may contain false positives, verify each finding

References

\[1\][https://www.owasp.org/index.php/OWASP\_Dependency\_Check](https://www.owasp.org/index.php/OWASP_Dependency_Check)  
\[2\][https://jeremylong.github.io/DependencyCheck/](https://jeremylong.github.io/DependencyCheck/)  
\[3\][https://wiki.jenkins-ci.org/display/JENKINS/OWASP+Dependency-Check+Plugin](https://wiki.jenkins-ci.org/display/JENKINS/OWASP+Dependency-Check+Plugin)

