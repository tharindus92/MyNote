# Jenkins windows slave service does not interact with desktop

I have followed this guide to install a jenkins slave on windows 8 as a service:

[https://wiki.jenkins-ci.org/display/JENKINS/Installing+Jenkins+as+a+Windows+service\#InstallingJenkinsasaWindowsservice-InstallSlaveasaWindowsservice%28require.NET2.0framework%29](https://wiki.jenkins-ci.org/display/JENKINS/Installing+Jenkins+as+a+Windows+service#InstallingJenkinsasaWindowsservice-InstallSlaveasaWindowsservice%28require.NET2.0framework%29)

I need to run a job that interact with the desktop \(run an application that opens a browser etc.\). So after I have installed the slave as a service \(running jnlp downloaded from the master\) I have changed the service "Log on" to "Allow to interact with display".

For some reason its only possible to enable this for the "Local System account" even though its recommended to run the service as a specified user, eg. jenkins.

But nothing happens when I execute the job, the browser is not opened. If I instead stop the service and just launch the slave through the jnlp file the job runs fine - the browser is opened.

Anybody had any luck interacting with the desktop when running a jenkins windows slave as a service?

 My Jenkins Service runs as user "jenkins" and all I did was to create Desktop folders in: **C:\Windows\system32\config\systemprofile\desktop** and if 64 bit Windows also in **C:\Windows\SysWOW64\config\systemprofile\desktop** - then it runs perfectly

