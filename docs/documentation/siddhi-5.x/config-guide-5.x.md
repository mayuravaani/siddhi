# Siddhi 5.x Config Guide 

## Configuring Datasources

<p>Siddhi Runner stores product-specific data in the H2 database located in the <code>&lt;SIDDHI_RUNNER_HOME&gt;/wso2/runner/database</code> directory by default.</p>

</p>This embedded H2 database is suitable for development, testing, and for some production environments. For most production environments, 
however, we recommend you to use an industry-standard RDBMS such as Oracle, PostgreSQL, MySQL, MS SQL. Most table schemas are self generated by the feature itself. 
For others, you can use the scripts provided with Siddhi Runner (in the <code>&lt;SIDDHI_RUNNER_HOME&gt;/wso2/runner/dbscripts</code> directory) to install and configure databases, 
including Oracle, PostgreSQL, MySQL and MS SQL. </p>

<p>The datasources are defined in the <code>&lt;SIDDHI_RUNNER_HOME&gt;/conf/runner/deployment.yaml</code> file.</p> 

!!! Note "Including database drivers."
    You need to include the database driver corresponding to the database in the <code>&lt;SIDDHI_RUNNER_HOME&gt;/lib/</code> directory for Siddhi to communicate with the database.

!!! Tip "Converting Non OSGI drivers."
    If the database driver is not an OSGI bundle, then it should be converted to OSGI. Please refer [Adding Third Party Non OSGi Libraries](http://siddhi.io/documentation/siddhi-5.x/config-guide-5.x/#adding-third-party-non-osgi-libraries) documentation.

<p>Below are the sample datasource configuration for each database type supported:</p>

<ul>
<li> MySQL </li>
  <script src="https://gist.github.com/pcnfernando/5911341d74f36a3f99252cc859a7f836.js"></script>

<li> Oracle </li>
<p>There are two ways to configure this database type. If you have a System Identifier (SID), use this (older) format:</p>
```
jdbc:oracle:thin:@[HOST][:PORT]:SID
```
   <script src="https://gist.github.com/pcnfernando/c32d6b0d12c49dfe2ea91100db9c728c.js"></script>

<p>If you have an Oracle service name, use this (newer) format:</p>
```
jdbc:oracle:thin:@//[HOST][:PORT]/SERVICE
```
<script src="https://gist.github.com/pcnfernando/35e7f82f9aceed3cb28531ac8e36699c.js"></script>

<li> PostgreSQL </li>
<script src="https://gist.github.com/pcnfernando/a48ce3c0422cd5db5657758e85f4bc9f.js"></script>

<li> MsSQL </li>
</ul>
<script src="https://gist.github.com/pcnfernando/b095e794ad753e11ca58b2a5bafe080e.js"></script>

## Configuring Periodic State Persistence

This section explains how to prevent the loss of data that can result from a system failure by persisting the state of 
Siddhi periodically either into a database system or into the file system.

### Prerequisites
Before configuring database persistence, the following prerequisites must be completed.

* One or more Siddhi Applications must be running in Siddhi Runner.
* A working RDBMS instance that can be used for data persistence must exist.
* The requirements of the datasource must be already defined.
* Database persistence involves updating the databases connected to Siddhi Runner with the latest information relating to the events that are being processed by Siddhi at a given time.

### Configuring Database System Persistence
<p>The supported databases are H2, MySQL, PostgreSQL, MSSQL and Oracle. The relevant jdbc driver jar should be downloaded and added to the <code>&lt;SIDDHI_RUNNER_HOME&gt;/lib</code> directory to prior to using database system persistence.</p>
<p>To configure periodic data persistence, update the <code>&lt;SIDDHI_RUNNER_HOME&gt;/conf/worker/deployment.yaml</code> file under <code>state.persistence</code> <span style="font-family: monospace;"> </span>as follows:</p>

| Parameter | Purpose | Required Value |
| ------------- |-------------|-------------|
| enabled | This enables data persistence. | true |
| intervalInMin | The time interval in minutes that defines the interval in which state of Siddhi applications should be persisted | 1 |
| revisionsToKeep | The number of revisions to keep in the system. When a new persist takes place, the old revisions are removed. | 3 |
| persistenceStore | The persistence store | io.siddhi.distribution.core.persistence.DBPersistenceStore |
| config > datasource | The datasource to be used in persisting the state. The provided datasource should be properly defined in the deployment.yaml. For detailed instructions of how to configure a datasource, see [Configuring Datasources](###Configuring Datasources).| SIDDHI_PERSISTENCE_DB (Datasource with this name should be defined in wso2.datasources) |
| config > table | The table that should be created and used for the persisting of the state. | PERSISTENCE_TABLE |

<p> The following is a sample segment of the required configurations in the <code>&lt;SIDDHI_RUNNER_HOME&gt;/conf/runner/deployment.yaml</code> file to configure file system persistence.</p>

<script src="https://gist.github.com/pcnfernando/7b94df53248fb626867bd7b64019a109.js"></script>

### Configuring File System Persistence
<p>This section explains how to persist the states of Siddhi applications during a required time interval in the file system in order to maintain back-ups. To configure state persistence, update the <code>&lt;SIDDHI_RUNNER_HOME&gt;/conf/runner/deployment.yaml</code> file under <code>state.persistence</code> <span> </span>as follows:</p>

| Parameter | Purpose | Required Value |
| ------------- |-------------|-------------|
| enabled | This enables data persistence. | true |
| intervalInMin | The time interval in minutes that defines the interval in which state of Siddhi applications should be persisted | 1 |
| revisionsToKeep | The number of revisions to keep in the system. When a new persist takes place, the old revisions are removed. | 3 |
| persistenceStore | The persistence store | io.siddhi.distribution.core.persistence.FileSystemPersistenceStore |
| config > location | A fully qualified folder location to where the revision files should be persisted. | siddhi-app-persistence |

<p>The following is a sample segment of the required configurations in the <code>&lt;SIDDHI_RUNNER_HOME&gt;/conf/runner/deployment.yaml</code> file to configure file system persistence.</p>
<script src="https://gist.github.com/pcnfernando/19f1879b86bd96a25762e2cadf8ae407.js"></script>

## Defining and Configuring Siddhi Extensions Externally

<p>Siddhi extensions cater usecase specific logics that are not out of the box available in Siddhi Streaming engine. 
You can define and configure these extensions in-line within the Siddhi application or externally in Siddhi Runner configuration file. </p>

### Defining Sources, Sinks and Tables Externally

<p> A source, sink or a table could be defined externally, and referred from several siddhi applications as described below.</p>
<p>Multiple sources can be defined in the <code>&lt;SIDDHI_RUNNER_HOME&gt;/conf/runner/deployment.yaml</code> file. </p>

<p>The following is a sample configuration of a source.</p>
<script src="https://gist.github.com/pcnfernando/323f593b9ae1e814cedc880892cdf26f.js"></script>

<p>You can refer to a source configured in the <code>&lt;SIDDHI_RUNNER_HOME&gt;/conf/runner/deployment.yaml</code> file from a Siddhi application as shown in the example below.</p>
```
@Source(ref='source1', basic.auth.enabled='false',
        @map(type='json', @attributes( name='$.name', amount='$.quantity')))
define stream SweetProductionStream (name string, amount double);
```
For detailed instructions to configure a source, see [Siddhi Guide - Source](http://siddhi-io.github.io/siddhi/documentation/siddhi-5.x/query-guide-5.x/#source)

### Configuring Siddhi Extensions Externally

<p>The pre-written Siddhi extensions supported by Siddhi Runner are configured with default values for system parameters. 
If you need to override those values, you can refer to those extensions from the <code>&lt;SIDDHI_RUNNER_HOME&gt;/conf/runner/deployment.yaml</code> file and 
add the system parameters with the required values as key-value pairs. </p>

<p>The following is a sample configuration of an extension.</p>
<script src="https://gist.github.com/pcnfernando/52f8720842bd5feca1481e1b5266b853.js"></script>

<p>For each separate extension you want to configure, add a sub-section named extension under the extensions subsection.</p>

<p>Following are examples for overriding default values for system properties.</p>

<p>Example 1: Defining host and port for TCP</p>
```
siddhi:
  extensions:
    - extension:
        name: tcp
        namespace: source
        properties:
          host: 0.0.0.0
          port: 5511
```
<p>Example 2: Overwriting the default RDBMS configuration</p>
```
siddhi:
  extensions:
    - extension:
        name: rdbms
        namespace: store
        properties:
          mysql.batchEnable: true
          mysql.batchSize: 1000
          mysql.indexCreateQuery: "CREATE INDEX {{TABLE_NAME}}_INDEX ON {{TABLE_NAME}} ({{INDEX_COLUMNS}})"
          mysql.recordDeleteQuery: "DELETE FROM {{TABLE_NAME}} {{CONDITION}}"
          mysql.recordExistsQuery: "SELECT 1 FROM {{TABLE_NAME}} {{CONDITION}} LIMIT 1"
```          

## Adding Extensions and Third Party Libraries

<p> The Siddhi extensions supported for Siddhi Runner are shipped with Siddhi Runner by default. 
If you need to download and install a different version of an extension or add an external dependency, 
follow the sections below broken down based on various environments. </p>
 
### Adding to Siddhi Java library
 
<p>You can add Siddhi extension jars and third-party libraries as dependencies when using Siddhi as a Java library.</p>
<p>Following is a sample <code>pom.xml</code> dependency snippet which includes Siddhi mandatory dependencies and <code>siddhi-io-http</code> extension.</p>

```xml
<!--Siddhi Mandatory Dependencies-->
 <dependency>
     <groupId>io.siddhi</groupId>
     <artifactId>siddhi-core</artifactId>
     <version>5.x.x</version>
   </dependency>
   <dependency>
     <groupId>io.siddhi</groupId>
     <artifactId>siddhi-query-api</artifactId>
     <version>5.x.x</version>
   </dependency>
   <dependency>
     <groupId>io.siddhi</groupId>
     <artifactId>siddhi-query-compiler</artifactId>
     <version>5.x.x</version>
   </dependency>
   <dependency>
     <groupId>io.siddhi</groupId>
     <artifactId>siddhi-annotations</artifactId>
     <version>5.x.x</version>
   </dependency>
   <dependency>
     <groupId>io.siddhi</groupId>
     <artifactId>siddhi-annotations</artifactId>
     <version>5.x.x</version>
   </dependency>
   
<!--Extension Dependencies-->
   <dependency>
     <groupId>org.wso2.extension.siddhi.io.http</groupId>
     <artifactId>siddhi-io-http</artifactId>
     <version>${siddhi.io.http.version}</version>
   </dependency>   
```

You can find a quick start guide in [Using Siddhi as a library](https://siddhi-io.github.io/siddhi/documentation/siddhi-5.x/user-guide-5.x/#using-siddhi-as-a-java-library) documentation.
 
### Adding to Siddhi Local Micro Service
<p>You can install the Siddhi extensions and third-party dependencies in your Siddhi Runner local pack by placing the JARs you downloaded in the <code>&lt;SIDDHI_RUNNER_HOME&gt;/lib</code> directory.

<p>Since Siddhi Runner is a OSGi-based product, when you integrate third party products such as Oracle with Siddhi Runner, you need to check whether the libraries you need to add to Siddhi Runner are OSGi based. If they are not, you need to convert them to OSGi bundles before adding them to the <code>&lt;SIDDHI_RUNNER_HOME&gt;/lib</code> directory.</p>
Please refer [Converting Third Party Non OSGi Libraries](http://siddhi.io/documentation/siddhi-5.x/config-guide-5.x/#converting-third-party-non-osgi-libraries) documentation.
 
### Adding to Siddhi Docker Micro Service

<p>You have to manually build a docker image using the below docker file bundling the needed extensions and third-party libraries.</p>
<script src="https://gist.github.com/pcnfernando/09d3af8f958c464425a892efabac2d98.js"></script>

Here, we use the siddhi-runner-base-alpine as the base image while creating the image. The parent/base image bundles the Alpine Linux OS, JDK and the Siddhi distribution.
You can find the Siddhi Runner docker build artifacts in [docker-siddhi repository](https://github.com/siddhi-io/docker-siddhi/tree/master/docker-files/siddhi-runner).

Copy the needed OSGIfied JARs and extensions to the ${BUNDLE_JAR_DIR} as defined in the above docker file, so that it would be bundled during the building phase to the docker image.
Please refer [Converting Third Party Non OSGi Libraries](http://siddhi.io/documentation/siddhi-5.x/config-guide-5.x/#converting-third-party-non-osgi-libraries) documentation to convert non OSGI bundles before bundling it to the image.

You can find a quick start guide in [Using Siddhi as a Docker microservice](https://siddhi-io.github.io/siddhi/documentation/siddhi-5.x/user-guide-5.x/#using-siddhi-as-a-docker-micro-service) documentation.

### Adding to Siddhi Kubernetes Micro Service

To use a Siddhi Runner distribution bundled with extensions and third party libraries, you have to first create a docker image following the steps described in [Adding to Docker Micro Service](https://siddhi-io.github.io/siddhi/documentation/siddhi-5.x/config-guide-5.x/#adding-to-docker-micro-service) documentation.
This created image can be referenced from the Siddhi Kubernetes artifacts thereafter.

You can find a quick start guide in [Using Siddhi as Kubernetes microservice](https://siddhi-io.github.io/siddhi/documentation/siddhi-5.x/user-guide-5.x/#using-siddhi-as-kubernetes-micro-service) documentation.

### Converting Third Party Non OSGi Libraries

<p>To convert jar files to OSGi bundles, follow the procedure given below:</p>
<ol>
<li>
Download the non-OSGi jar for the required third party product, and save it in a preferred directory in your machine.
</li>
<li>
In your CLI, navigate to the <code>&lt;SIDDHI_RUNNER_HOME&gt;/bin</code> directory. Then issue the following command.

```
./jartobundle.sh <PATH_TO_NON-OSGi_JAR> ../lib
```

This generates the converted file in the <code>&lt;SIDDHI_RUNNER_HOME&gt;/lib</code> directory.
</li>
<li>
Restart the Siddhi Runner.
</li>
</ol>
