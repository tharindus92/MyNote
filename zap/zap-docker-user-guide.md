# ZAP Docker User Guide

## Introduction <a id="introduction"></a>

Docker image with OWASP Zed Attack Proxy preinstalled.

## Details <a id="details"></a>

### Install Instructions: <a id="install-instructions"></a>

For the stable release:

```text
docker pull owasp/zap2docker-stable
```

For the latest weekly release:

```text
docker pull owasp/zap2docker-weekly
```

For the live release \(built whenever the [zaproxy](https://github.com/zaproxy/zaproxy) project is changed\):

```text
docker pull owasp/zap2docker-live
```

For the bare release \(a very small Docker image, contains only the necessary required dependencies to run ZAP, ideal for CI environments\):

```text
docker pull owasp/zap2docker-bare
```

The Dockerfiles can be found [here](https://github.com/zaproxy/zaproxy/tree/main/docker).

#### Healthcheck <a id="healthcheck"></a>

The docker file now supports [healthcheck](https://docs.docker.com/engine/reference/builder/#healthcheck). The check uses the `zap-cli status` to check that ZAP completed loading. If you are running ZAP with port other than the default `8080`, you need to set the `ZAP_PORT` environment variable. Otherwise, the healthcheck will fail.

### Usage Instructions: <a id="usage-instructions"></a>

#### Packaged Scans: <a id="packaged-scans"></a>

All of the docker images provide a set of packaged scan scripts:

* [Baseline Scan](https://www.zaproxy.org/docs/docker/baseline-scan/) which runs the ZAP spider against the target for \(by default\) 1 minute followed by an optional ajax spider scan before reporting the results of the passive scanning.
* [Full Scan](https://www.zaproxy.org/docs/docker/full-scan/) which runs the ZAP spider against the target \(by default with no time limit\) followed by an optional ajax spider scan and then a full active scan before reporting the results.
* [API Scan](https://www.zaproxy.org/docs/docker/api-scan/) which performs an active scan against APIs defined by OpenAPI, or GraphQL \(post 2.9.0\) via either a local file or a URL.

In all cases the scans are tuned by:

* Disabling the Db recovery log
* Disabling all tags
* Reporting a maximum of 10 passive scan alert instances

The `zap_tuned()` [Scan Hook](https://www.zaproxy.org/docs/docker/scan-hook) is called after these changes have been made so you can undo them or apply other changes at this point if you want.

#### GitHub Actions: <a id="github-actions"></a>

The following GitHub Actions wrap 2 of the above packaged scans and also support raising GitHub issues for potential vulnerabilities found:

* [OWASP ZAP Baseline Scan](https://github.com/marketplace/actions/owasp-zap-baseline-scan)
* [OWASP ZAP Full Scan](https://github.com/marketplace/actions/owasp-zap-full-scan)

For more details see the blog posts:

* [Automate Security Testing with ZAP and GitHub Actions](https://www.zaproxy.org/blog/2020-04-09-automate-security-testing-with-zap-and-github-actions/)
* [Dynamic Application Security Testing with ZAP and GitHub Actions](https://www.zaproxy.org/blog/2020-05-15-dynamic-application-security-testing-with-zap-and-github-actions/)

#### ZAP GUI in a Browser: <a id="zap-gui-in-a-browser"></a>

Yes, you can run the ZAP Desktop GUI in a browser. You can use it in just the same way as the Swing UI and can even proxy via it. See the [Webswing](https://www.zaproxy.org/docs/docker/webswing/) page for details.

#### ZAP Headless: <a id="zap-headless"></a>

You can also start the ZAP in headless mode with following command:

```text
docker run -u zap -p 8080:8080 -i owasp/zap2docker-stable zap.sh -daemon -host 0.0.0.0 -port 8080 -config api.addrs.addr.name=.* -config api.addrs.addr.regex=true -config api.key=<api-key>
```

**Note**: `-config api.addrs.addr.name=.*` opens the API up for connections from any other host, it is prudent to configure this more specifically for your network/setup.

#### ZAP Headless with xvfb: <a id="zap-headless-with-xvfb"></a>

You can start the ZAP in headless mode with xvfb following command:

```text
docker run -u zap -p 8080:8080 -i owasp/zap2docker-stable zap-x.sh -daemon -host 0.0.0.0 -port 8080 -config api.addrs.addr.name=.* -config api.addrs.addr.regex=true
```

**Note**: `-config api.addrs.addr.name=.*` opens the API up for connections from any other host, it is prudent to configure this more specifically for your network/setup.

This first starts xvfb \(X virtual frame buffer\) which allows add-ons that use Selenium \(like the Ajax Spider and DOM XSS scanner\) to run in a headless environment. Firefox is also installed so can be used with these add-ons.

#### ZAP CLI: <a id="zap-cli"></a>

[ZAP CLI](https://github.com/Grunny/zap-cli) is a ZAP wrapper written in Python. It provides a simple way to do scanning from the command line:

```text
docker run -i owasp/zap2docker-stable zap-cli quick-scan --self-contained \
    --start-options '-config api.disablekey=true' http://target
```

#### ZAPR: <a id="zapr"></a>

Zapr is ruby script for ZAP which allows commandline active scanning for desired target:

```text
docker run -u zap -i owasp/zap2docker-stable zapr --debug --summary http://target
```

#### Accessing the API from outside of the Docker container: <a id="accessing-the-api-from-outside-of-the-docker-container"></a>

Docker appears to assign ‘random’ IP addresses, so an approach that appears to work is:

Run ZAP as a daemon listening on “0.0.0.0”:

```text
docker run -p 8090:8090 -i owasp/zap2docker-stable zap.sh -daemon -port 8090 -host 0.0.0.0
```

Find out the container id:

```text
docker ps
```

Find out which address has been assigned to it:

```text
docker inspect <CONTAINER ID> | grep IPAddress
```

You should be then able to point your browser at the specified host/port and access the ZAP API, eg http://172.17.0.8:8090/

Note that on Macs the IP will be the IP of the Docker VM host. This is accessible with:

```text
docker-machine ip <host>
```

#### Scanning an app running on the host OS <a id="scanning-an-app-running-on-the-host-os"></a>

IP addresses like localhost and 127.0.0.1 cannot be used to access an app running on the host OS from within a docker container. To get around this you can use the following code to get an IP address that will work:

```text
$(ip -f inet -o addr show docker0 | awk '{print $4}' | cut -d '/' -f 1)
```

For example:

```text
docker run -t owasp/zap2docker-weekly zap-baseline.py -t http://$(ip -f inet -o addr show docker0 | awk '{print $4}' | cut -d '/' -f 1):10080
```

#### Scanning an app running in another Docker container <a id="scanning-an-app-running-in-another-docker-container"></a>

By default Docker does not allow apps running in one container to access an app running in another container. To get around this restriction create a Docker network using:

```text
docker network create zapnet
```

And then include the Docker option `--net zapnet` when starting both your target app and the ZAP packaged scan.

