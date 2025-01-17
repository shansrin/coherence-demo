# Oracle Coherence Demonstration Application

## Overview

This document describes how to build and run the Coherence Demonstration application. The application showcases Coherence general features, scalability capabilities, and new features of 12.2.1 version including:

* Cache Persistence
* Federation
* Java 8 Support

You can run the Coherence demonstration application either locally or through Kubernetes using the [Oracle Coherence Operator](https://github.com/oracle/coherence-operator).

When you run the application locally, it results in a single self-contained JAR, javadoc and source.

The demonstration uses AngularJS 1.7.5, Bootstrap 3.3.4 and a number of other frameworks. The UI interacts with Coherence using the REST API.

> **Note:** To run this demonstration locally, you require Oracle Coherence 12.2.1.3.0 version.

## Table of Contents

- [Oracle Coherence Demonstration Application](#oracle-coherence-demonstration-application)
  * [Overview](#overview)
  * [Table of Contents](#table-of-contents)
  * [Prerequisites](#prerequisites)
    + [General Prerequisites](#general-prerequisites)
    + [Environment Variables](#environment-variables)
    + [Coherence JARs](#coherence-jars)
    + [Kubernetes Prerequisites](#kubernetes-prerequisites)
    + [Get Coherence Docker Image](#pull-coherence-docker-image)
    + [Enable Kubernetes in Docker](#enable-kubernetes-in-docker)
  * [Run the Application](#run-the-application)
    + [Run the Application Locally](#run-the-application-locally)
      - [Modify the Defaults](#modify-the-defaults)
    + [Run the Application on Kubernetes (Coherence 12.2.1.3.x)](#run-the-application-on-kubernetes-coherence-12213x)
    + [Enable Federation on Kubernetes](#enable-federation-on-kubernetes)
    + [Uninstalling the Charts](#uninstalling-the-charts)
  * [References](#references)

## Prerequisites

### General Prerequisites

To run the demonstration application, you must have the following software installed:

1. Java 8 or 11 SE Development Kit or Runtime environment.

   You can download JDK 8 from:
   - [Java SE Development Kit](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
   - [JAVA SE Runtime Environment](http://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html)

   You can download JDK 11 from:
   - [Java SE Development Kit 11 Downloads](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html)

2. Maven 3.5.4 or later version installed and configured.

3. Oracle Coherence 12.2.1.3.0 or later version installed.

   You can download it from http://www.oracle.com/technetwork/middleware/coherence/downloads/index.html.

   If you want to demonstrate the Coherence VisualVM plug-in, follow the instructions to install:
   https://docs.oracle.com/middleware/12213/coherence/manage/using-jmx-manage-oracle-coherence.htm#COHMG5583
4. VisualVM is not included in JDK 11. You can download and install VisualVM from [https://visualvm.github.io](https://visualvm.github.io). You must provide `-Dvisualvm.executable` to point to the VisualVM executable.
5. Use a web browser that supports AngularJS to run the application. The following browsers are supported:
   * Safari, Chrome, Firefox, Opera 15, IE9 and mobile browsers (Android, Chrome Mobile, iOS Safari).
For more information about browser compatibility, see https://code.angularjs.org/1.4.1/docs/misc/faq.

> **Note**: All code compiles to JDK 8 bytecode for compatibility with Coherence releases.

### Environment Variables

Ensure that the following environment variables are set in your configuation:

* `JAVA_HOME` -- This variable must point to the location of the JDK version supported by the Oracle Coherence version that you use. Ensure that the path is set accordingly:</br>
For Linux/UNIX OS:
```bash
export PATH=$JAVA_HOME/bin:$PATH
```
For Windows OS:
```bash
set PATH=%JAVA_HOME%\bin;%PATH%
```

* `COHERENCE_HOME` -- This variable must point to the `\coherence` directory of your Coherence installation. This is required for the Maven `install-file` commands.

* `MAVEN_HOME` -- If `mvn` command is not set in your PATH variable, then set `MAVEN_HOME` directed to the `bin` folder of Maven installation and then add `MAVEN_HOME\bin` to your PATH variable list.

### Coherence JARs

You must have Coherence and Coherence-REST installed into your local maven repository. If not, execute the following commands with the version number of Coherence that you have installed in your configuration.

For Linux/UNIX/Mac OS:

```bash
mvn install:install-file -Dfile=$COHERENCE_HOME/lib/coherence.jar      -DpomFile=$COHERENCE_HOME/plugins/maven/com/oracle/coherence/coherence/12.2.1/coherence.12.2.1.pom
mvn install:install-file -Dfile=$COHERENCE_HOME/lib/coherence-rest.jar -DpomFile=$COHERENCE_HOME/plugins/maven/com/oracle/coherence/coherence-rest/12.2.1/coherence-rest.12.2.1.pom
```

If you are using Coherence 12.2.1.4.0 or later version, run the following commands:
```bash
$ mvn install:install-file -Dfile=$COHERENCE_HOME/lib/coherence-http-grizzly.jar -DpomFile=$COHERENCE_HOME/plugins/maven/com/oracle/coherence/coherence-http-grizzly/12.2.1/coherence-http-grizzly.12.2.1.pom
```

For Windows OS:

```bash
mvn install:install-file -Dfile=%COHERENCE_HOME%\lib\coherence.jar      -DpomFile=%COHERENCE_HOME%\plugins\maven\com\oracle\coherence\coherence\12.2.1\coherence.12.2.1.pom
mvn install:install-file -Dfile=%COHERENCE_HOME%\lib\coherence-rest.jar -DpomFile=%COHERENCE_HOME%\plugins\maven\com\oracle\coherence\coherence-rest\12.2.1\coherence-rest.12.2.1.pom
```

### Kubernetes Prerequisites

To run the application in Kubernetes using the Coherence Operator, you must
ensure the following requirements:

* Check [Software Requirements](https://github.com/oracle/coherence-operator/blob/gh-pages/docs/quickstart.md#software-requirements) and [Runtime Environment Requirements](https://github.com/oracle/coherence-operator/blob/gh-pages/docs/quickstart.md#runtime-environment-requirements) in the *Quick Start Guide*.

* Add the Helm repository. Execute the following commands to create a `coherence` helm repository:

  ```bash
   $ helm repo add coherence https://oracle.github.io/coherence-operator/charts
   $ helm repo update
   ```

### Get Coherence Docker Image

Get the Coherence Docker image from the Oracle Container Registry:

1. In a web browser, navigate to [Oracle Container Registry](https://container-registry.oracle.com) and click Sign In.
2. Enter your Oracle credentials or create an account if you don't have one.
3. Search for coherence in the Search Oracle Container Registry field.
4. Click `coherence` in the search result list.
5. On the Oracle Coherence page, select the language from the drop-down list and click **Continue**.
6. Click **Accept** on the Oracle Standard Terms and Conditions page.

This action is required to allow the image to be pulled automatically when required using the Kubernetes secret.

### Enable Kubernetes in Docker

Ensure that your local Kubernetes is enabled. If you are running Docker using Docker Desktop, select **Enable Kubernetes** in the Settings menu.

## Run the Application

The Coherence Demonstration Application can be run locally or through Kubernetes using the [Oracle Coherence Operator](https://github.com/oracle/coherence-operator).

### Run the Application Locally

Build the application using Maven:

   ```bash
   $ mvn clean install
   ```
 The `target` directory contains a list of files:
```bash
 coherence-demo-{version}-SNAPSHOT.jar          - Executable JAR file, see instructions below
 coherence-demo-{version}-SNAPSHOT-javadoc.jar  - javadoc
 coherence-demo-{version}-SNAPSHOT-sources.jar  - sources
 ```

Run the JAR file in the `target` directory:

```bash
$ java -jar target/coherence-demo-3.0.0-SNAPSHOT.jar
```
If you are using JDK 11, use the following argument to start the VisualVM while running the JAR file:
```bash
java -Dvisualvm.executable=/u01/oracle/product/visualvm/visualvm_143/bin/visualvm -jar target/coherence-demo-3.0.0-SNAPSHOT.jar
```
`-Dvisualvm.executable=/u01/oracle/product/visualvm/visualvm_143/bin/visualvm` in the command points to the path of the VisualVM executable.


A Coherence Cache server and HTTP server are started on port 8080 for serving REST and application data. When the Cache server starts, the application loads on the default web browser at http://127.0.0.1:8080/application/index.html.

The following features are available to demonstrate in the application:

* Dynamically add or remove cluster members and observe the data repartition and recover automatically.
* Create and recover snapshots from the **Persistence** menu.
* Enable real-time price updates.
* Enable or disable indexes for queries.
* Add additional data, clear the cache or populate the cache from the **Tools** menu.
* Start VisualVM from the **Tools** menu.
* Start a secondary cluster from the **Federation** menu.
* Pause and resume replication to secondary cluster.
* Issue replicate all to secondary cluster.
* Open secondary cluster dashboard to observe changes are replicated.
* Stop Federation and shut down secondary cluster.


> **Note:** If you recover a snapshot on a cluster, you must replicate all to resynchronize.

To shut down the application, select **Shutdown** option from the **Tools** menu. This shuts down all the processes including the secondary cluster if started.

> **Note:** Secondary cluster will not form if you are running on a virtual private network due to security restrictions.

#### Modify the Defaults

**HTTP Ports and Hostname**

The default HTTP hostname is 127.0.0.1 and default port is 8080. To modify these you can add the `http.hostname` or `http.port` properties on startup:

```bash
$ java -Dhttp.hostname=myhostname -Dhttp.port=9000 -jar coherence-demo-3.0.0-SNAPSHOT.jar
```
By changing the `http.hostname` you can access the application outside of
your local machine.

**Default Cluster Names**

When starting up the application, the timezone is analyzed and default names are selected for the primary and secondary cluster (see [Launcher.java](https://github.com/coherence-community/coherence-demo/tree/master/src/main/java/com/oracle/coherence/demo/application/Launcher.java)). If you want to customize the name, do the following:

```bash
$ java -Dprimary.cluster=NewYork -Dsecondary.cluster=Boston -jar coherence-demo-3.0.0-SNAPSHOT.jar
```
If you want to use a cluster name with a space, you must enclose it in quotes.

### Run the Application on Kubernetes (Coherence 12.2.1.3.x)

The steps to run the application on Kubernetes comprises the following Helm chart installs:
* Oracle Coherence Operator
* Coherence Cluster - storage-enabled Coherence servers
* Coherence Application Tier - storage-disabled with Grizzly HTTP Server

> **Note:** If you want to enable Federation when running on Kubernetes, see [Enable Federation on Kubernetes](#enable-federation-on-kubernetes).


1. **Create Namespace**</br>
   Run the application using the Oracle Coherence Operator in a namespace
called `coherence-demo-ns`. Create the demonstration namespace:
   ```bash
   $ kubectl create namespace coherence-demo-ns

   namespace/sample-coherence-ns created
   ```
2. **Create Secret** </br>
   Create a secret for pulling the images from private repositories. For this application, create a secret named `coehrence-demo-secret` in the namespace `coherence-demo-ns`.
   ```bash
   $ kubectl create secret docker-registry coherence-demo-secret \
        --namespace coherence-demo-ns \
        --docker-server=your-docker-server \
        --docker-username=your-docker-username \
        --docker-email=your-email-address \
        --docker-password=your-docker-password
   ```
   > **Note**: By default, the Docker details must be your Oracle Container Registry details.

   See [https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/) for more information.
3. **Build and Push Sidecar Docker Image**</br>
Build and push the sidecar Docker image(optional). The Oracle Coherence Operator requires a sidecar Docker image to be built containing the classes and configuration files required by the application.

  Ensure that you have Docker running locally and execute the following command:

   ```bash
   $ mvn clean install -P docker
   ```

   This creates an image named `coherence-demo-sidecar:3.0.0-SNAPSHOT` which contains cache configuration and Java classes to be added to the classpath at runtime.

   > Note: If you are running against a remote Kubernetes cluster, you need to push the sidecar Docker image to your repository accessible to that cluster. You also need to prefix the image name in your `helm` commands below.

4. **Install the Oracle Coherence Operator**</br>
  Install the operator using `helm`:

   ```bash
   $ helm install \
      --namespace coherence-demo-ns \
      --set imagePullSecrets=coherence-demo-secret \
      --name coherence-operator \
      --set "targetNamespaces={coherence-demo-ns}" \
      coherence/coherence-operator
   ```
   Confirm the creation of the chart:

   ```bash
   $ helm ls
   NAME              	REVISION	UPDATED                 	STATUS  	CHART                   	APP VERSION	NAMESPACE               
   coherence-operator	1       	Fri May 24 14:39:15 2019	DEPLOYED	coherence-operator-0.9.4	0.9.4      	coherence-demo-ns

   $ kubectl get pods -n coherence-demo-ns
   NAME                                  READY   STATUS    RESTARTS   AGE
   coherence-operator-5d4dc4546c-4c925   1/1     Running   0          50s
   ```

   To enable log capture or metrics, see [here](https://oracle.github.io/coherence-operator/docs/samples/#list-of-samples) for the list of samples.

5. **Install the Coherence Cluster**</br>
  Install the Coherence cluster:

   ```bash
   $ helm install \
      --namespace coherence-demo-ns \
      --name coherence-demo-storage \
      --set clusterSize=1 \
      --set cluster=PrimaryCluster \
      --set imagePullSecrets=coherence-demo-secret \
      --set store.cacheConfig=cache-config.xml \
      --set store.pof.config=pof-config.xml \
      --set store.javaOpts="-Dwith.http=false" \
      --set store.maxHeap=512m \
      --set userArtifacts.image=coherence-demo-sidecar:3.0.0-SNAPSHOT \
      coherence/coherence
   ```
   > **Note:** By default, the latest version of the chart is used. You can add `--version 1.0.0` to select a specific version. For example, 1.0.0 is used in the command.

   You can also choose a specific version of the Coherence Docker image by specifying the following in the `helm install` command:

   ```bash
   --set coherence.image=container-registry.oracle.com/middleware/coherence:12.2.1.4.0
   ```
   Use `kubectl get pods -n coherence-demo-ns` to ensure that the pod is running. The pod storage-coherence-0 must be running and ready as shown:

   ```bash
   NAME                 READY   STATUS    RESTARTS   AGE
   coherence-demo-storage-0     1/1     Running   0          4m
   ```
   If the pod does not show as `Running`, you can use the following command to diagnose and troubleshoot the pod:

   ```bash
   $ kubectl describe pod coherence-demo-storage-0 -n coherence-demo-ns
   ```

6. **Install the Application Tier**</br>
  Install the application tier using the following command:

   ```bash
   $ helm install \
      --namespace coherence-demo-ns \
      --name coherence-demo-app \
      --set clusterSize=1 \
      --set cluster=PrimaryCluster \
      --set imagePullSecrets=coherence-demo-secret \
      --set store.cacheConfig=cache-config.xml \
      --set store.pof.config=pof-config.xml \
      --set store.wka=coherence-demo-storage-headless \
      --set store.javaOpts="-Dcoherence.distributed.localstorage=false"  \
      --set store.maxHeap=512m \
      --set userArtifacts.image=coherence-demo-sidecar:3.0.0-SNAPSHOT \
      coherence/coherence
   ```  

7. **Port Forward the HTTP Port**

   ```bash
   $ kubectl port-forward --namespace coherence-demo-ns coherence-demo-app-0 8080:8080
   ```  

8. **Access the Application**</br>

   Use the following URL to access the application home page:

   [http://127.0.0.1:8080/application/index.html](http://127.0.0.1:8080/application/index.html)  

9. **Scale the Appication**</br>
  Scale the application using `kubectl`. When running the application in Kubernetes, the **Add Server** and **Remove Server** options are not available. You need to use `kubectl` to scale the application.

   Scale the application to two nodes:

   ```bash
   $ kubectl scale statefulsets coherence-demo --namespace coherence-demo-ns --replicas=2
   ```

### Enable Federation on Kubernetes

You must use Oracle Coherence 12.2.1.4.0 or later for Federation to work within Kubernetes.

The setup for this example uses two Coherence clusters in the same Kubernetes cluster. If you want to use Federation across Kubernetes cluster, see the [Oracle Coherence Operator Samples](https://oracle.github.io/coherence-operator/docs/samples/#list-of-samples).

* Primary Cluster
  * Release name: cluster-1
  * Cluster name: PrimaryCluster
* Secondary Cluster
  * Release name: cluster-2
  * Cluster name: SecondaryCluster

> **Note**: For this Federation example, the installation is simplified by using the storage-enabled pods to also serve the HTTP requests.

1. **Create Namespace**</br>
   Run the application using the Oracle Coherence Operator in a namespace
called `coherence-demo-ns`. Create the demonstration namespace:
   ```bash
   $ kubectl create namespace coherence-demo-ns

   namespace/sample-coherence-ns created
   ```
2. **Create Secret** </br>
   Create a secret for pulling the images from private repositories. For this application, create a secret named `coherence-demo-secret` in the namespace `coherence-demo-ns`.
   ```bash
   $ kubectl create secret docker-registry coherence-demo-secret \
        --namespace coherence-demo-ns \
        --docker-server=your-docker-server \
        --docker-username=your-docker-username \
        --docker-email=your-email-address \
        --docker-password=your-docker-password
   ```
   > **Note**: By default, the Docker details must be your Oracle Container Registry details.
   See [https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/) for more information.

3. Build the sidecar image:

   ```bash
   $ mvn clean install -P coherence12214,docker -Dcoherence.version=12.2.1-4-0
   ```         
   > **Note:** The `coherence.version` must be set to your installed Coherence version.

4. Install the **Primary** cluster:

   ```bash
   $ helm install \
      --namespace coherence-demo-ns \
      --name cluster-1 \
      --set clusterSize=1 \
      --set cluster=PrimaryCluster \
      --set imagePullSecrets=coherence-demo-secret \
      --set store.cacheConfig=cache-config-12214.xml \
      --set store.overrideConfig=tangosol-coherence-override-12214.xml \
      --set store.pof.config=pof-config.xml \
      --set store.maxHeap=512m \
      --set store.javaOpts="-Dprimary.cluster=PrimaryCluster -Dprimary.cluster.port=40000 -Dprimary.cluster.host=cluster-1-coherence-headless -Dsecondary.cluster=SecondaryCluster -Dsecondary.cluster.port=40000 -Dsecondary.cluster.host=cluster-2-coherence-headless"  \
      --set store.ports.federation=40000 \
      --set userArtifacts.image=coherence-demo-sidecar:3.0.0-SNAPSHOT \
      --set coherence.image=your-12.2.1.4.0-Coherence-image \
      coherence/coherence
   ```   
   Replace `your-12.2.1.4.0-Coherence-image` with a Coherence 12.2.1.4.0 image such as `container-registry.oracle.com/middleware/coherence:12.2.1.4.0` when it is released.

5. Port forward the Primary Cluster - Port **8088**

   ```bash
   $ kubectl port-forward --namespace coherence-demo-ns cluster-1-coherence-0 8080:8080
   ```
   Use the following URL to access the application home page:

   [http://127.0.0.1:8080/application/index.html](http://127.0.0.1:8080/application/index.html)  

6. Install the **Secondary** cluster

   ```bash
   $ helm install \
      --namespace coherence-demo-ns \
      --name cluster-2 \
      --set clusterSize=1 \
      --set cluster=SecondaryCluster \
      --set imagePullSecrets=coherence-demo-secret \
      --set store.cacheConfig=cache-config-12214.xml \
      --set store.overrideConfig=tangosol-coherence-override-12214.xml \
      --set store.pof.config=pof-config.xml \
      --set store.javaOpts="-Dwith.data=false -Dprimary.cluster=PrimaryCluster -Dprimary.cluster.port=40000 -Dprimary.cluster.host=cluster-1-coherence-headless -Dsecondary.cluster=SecondaryCluster -Dsecondary.cluster.port=40000 -Dsecondary.cluster.host=cluster-2-coherence-headless"  \
      --set store.ports.federation=40000 \
      --set userArtifacts.image=coherence-demo-sidecar:3.0.0-SNAPSHOT \
      --set coherence.image=your-12.2.1.4.0-Coherence-image \
      coherence/coherence
   ```   

7. Port forward the Secondary Cluster - Port **8090**

   ```bash
   $ kubectl port-forward --namespace coherence-demo-ns cluster-2-coherence-0 8090:8080
   ```
   Use the following URL to access the application home page:

   [http://127.0.0.1:8090/application/index.html](http://127.0.0.1:8090/application/index.html)  

   You can see that there is no data in the Secondary cluster as the Federation is not yet started.

8. Start Federation on the Primary Cluster

   In the Primary Cluster, select **Start Federation** from **Federation** menu.
   Access the Secondary Cluster dashboard and you can see the data appearing from the Primary Cluster.   

### Uninstalling the Charts

Execute the following commands to delete the chart installed in this sample:

**Without Federation**

```bash
$ helm delete coherence-operator coherence-demo-storage coherence-demo-app --purge
```

**With Federation**

```bash
$ helm delete coherence-operator cluster-1 cluster-2 --purge
```

Before starting another sample, ensure that all the pods are removed from the previous sample.

If you want to remove the `coherence-operator`, then include it in the `helm delete` command.


## References

For more information about Oracle Coherence, see the following links:

* Download Coherence - [http://www.oracle.com/technetwork/middleware/coherence/downloads/index.html](http://www.oracle.com/technetwork/middleware/coherence/downloads/index.html)
* Coherence Documentation - [https://docs.oracle.com/middleware/12213/coherence/docs.htm](https://docs.oracle.com/middleware/12213/coherence/docs.htm)
* Coherence Community - [http://coherence.oracle.com/](http://coherence.oracle.com/)
* Coherence Operator GitHub Page - [https://github.com/oracle/coherence-operator](https://github.com/oracle/coherence-operator)
* Coherence Operator Documentation - [https://oracle.github.io/coherence-operator/](https://oracle.github.io/coherence-operator/)
