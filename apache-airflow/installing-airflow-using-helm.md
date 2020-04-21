# Installing airflow using helm

Data engineering is a difficult job and tools like airflow make that streamlined. Let’s take a look at how to get up and running with airflow on kubernetes.

## Prerequisites <a id="prerequisites"></a>

1. A kubernetes cluster - You can spin up on [AWS](https://aws.amazon.com/eks/), [GCP](https://cloud.google.com/kubernetes-engine/), Azure or digitalocean or you can start one on your local machine using [minikube](https://kubernetes.io/docs/setup/minikube/)
2. Helm - If you do not already have helm installed then follow [this](https://prabhatsharma.in/blog/helm-tutorial-the-package-manager-for-kubernetes-part-1) tutorial to get it installed

## Installing airflow using helm <a id="installing-airflow-using-helm"></a>

### 1. Easy way <a id="1-easy-way"></a>

```text
helm install stable/airflow
```

This will install airflow with default settings and a random release name. You are up and running with basic installation.

Congratulations!!!

### 2. Sophisticated way <a id="2-sophisticated-way"></a>

Chances are that for anything serious you would want to make changes to default installation.

We will make following changes to make our airflow installation useful in a n enterprise setting:

1. Create a namespace
2. Change the source of DAG files in the helm chart
3. Set up Active Directory authentication for airflow \(Optional\)

Let’s expand each of the above steps

#### 2.1 Create a namespace <a id="2-1-create-a-namespace"></a>

```text
kubectl create namespace airflow
```

switch to the namespace

> kubectl config set-context &lt;context-name&gt; –namespace=airflow

e.g.

```text
kubectl config set-context dev1 --namespace=airflow
```

#### 2.2 clone the charts repository: <a id="2-2-clone-the-charts-repository"></a>

Since we are going to be using helm chart we will use values.yaml to make required changes.

```text
git clone https://github.com/helm/charts.git
cd charts/stable/airflow
```

We want to make following changes:

1. Get the code for our DAGS from our git repo. I have a sample repo at [https://github.com/prabhatsharma/airflow-dags](https://github.com/prabhatsharma/airflow-dags).
2. Setup postgres for airflow
3. Set up authentication using microsoft Active Directory.

Open values.yaml in a text editor and modify following sections:

#### 2.3 Get the code for our DAGS from our git repo <a id="2-3-get-the-code-for-our-dags-from-our-git-repo"></a>

|  |  |
| :--- | :--- |


to \(see line 325\)

|  |  |
| :--- | :--- |


#### 2.4 Set up postgres <a id="2-4-set-up-postgres"></a>

Airflow needs postgres to store state. You will want to setup a postgres if you have one already installed. If you don’t have one already installed then you can let the chart install one for you.

|  |  |
| :--- | :--- |


#### 2.5 Active directory authentication - Create image <a id="2-5-active-directory-authentication-create-image"></a>

We will need ldap3 module to be installed in the airflow image. However default image [puckel/docker-airflow](https://hub.docker.com/r/puckel/docker-airflow/) does not have airflow installed. so we will have to build our own image.

We will fork and clone this repo [https://github.com/puckel/docker-airflow](https://github.com/puckel/docker-airflow) and add ldap3 module

```text
git clone https://github.com/puckel/docker-airflow
cd docker-airflow
```

Open Dockerfile in a text editor and add ldap3 module

|  |  |
| :--- | :--- |


Now build the image

```text
docker build . -t hiprabhat/airflow:latest 
docker push hiprabhat/airflow:latest
```

Replace the name of your docker repo from hiprabhat/airflow

Now update the image section in values.yaml to:

|  |  |
| :--- | :--- |


#### 2.6 Active directory authentication - set up configuration <a id="2-6-active-directory-authentication-set-up-configuration"></a>

I already have an AD installed that I would be using.

|  |  |
| :--- | :--- |


#### 2.7 Now lets begin installation <a id="2-7-now-lets-begin-installation"></a>

```text
helm install stable/airflow -f values.yaml
```

#### 2.8 Access you airflow installation <a id="2-8-access-you-airflow-installation"></a>

Let’s check the services created by our helm chart:

```text
kubectl get service
```

Output:

```text
NAME                   TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
airflow-flower         ClusterIP   10.100.132.31    <none>        5555/TCP   30s
airflow-postgresql     ClusterIP   10.100.111.183   <none>        5432/TCP   30s
airflow-redis-master   ClusterIP   10.100.89.235    <none>        6379/TCP   30s
airflow-web            ClusterIP   10.100.9.65      <none>        8080/TCP   30s
airflow-worker         ClusterIP   None             <none>        8793/TCP   30s
```

Now let’s use port-forward to access the service

```text
kubectl port-forward service/airflow-web 8080:8080
```

Now point your browser to [http://localhost:8080](http://localhost:8080/)

![AIRFLOW LOGIN PAGE](https://prabhatsharma.in/images/blog/airflow-login-page-small.png)

You can setup an ingress in front of your airflow-web service to make it accessible over internet.

Congratulations You are up and running with airflow!!!

You might also want to read the new blog on [Installing Airflow on Kubernetes Using Operator](https://prabhatsharma.in/blog/installing-airflow-on-kubernetes-using-operator)

