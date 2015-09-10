Apache Spark 1.5 on Docker
==========

This repository contains a Docker file to build a Docker image with Apache Spark 1.5. This is essentially a hacked fork of the well-known sequenceiq/spark images, so sequenceiq deserves all of the credit.

##Pull the image from Docker Repository
```
docker pull mpeskin/spark:1.5.0
```

## Building the image
```
first download spark-1.5.0-bin-hadoop2.6.tgz from the Spark download site and copy it to the project directory containing the Dockerfile. This is an unfortunate hack needed to get around corporate firewall issues. Then:

docker build --rm -t mpeskin/spark:1.5.0 .
```

## Running the image

* if using boot2docker make sure your VM has more than 2GB memory
* in your /etc/hosts file add $(boot2docker ip) as host 'sandbox' to make it easier to access your sandbox UI
* open yarn UI ports when running container
```
docker run -it -p 8088:8088 -p 8042:8042 -h sandbox mpeskin/spark:1.5.0 bash
```
or
```
docker run -d -h sandbox mpeskin/spark:1.5.0 -d
```

## Versions
```
Hadoop 2.6.0 and Apache Spark v1.5.0 on Centos 
```

## Testing

There are two deploy modes that can be used to launch Spark applications on YARN.

### YARN-client mode

In yarn-client mode, the driver runs in the client process, and the application master is only used for requesting resources from YARN.

```
# run the spark shell
spark-shell \
--master yarn-client \
--driver-memory 1g \
--executor-memory 1g \
--executor-cores 1

# execute the the following command which should return 1000
scala> sc.parallelize(1 to 1000).count()
```
### YARN-cluster mode

In yarn-cluster mode, the Spark driver runs inside an application master process which is managed by YARN on the cluster, and the client can go away after initiating the application.

Estimating Pi (yarn-cluster mode):

```
# execute the the following command which should write the "Pi is roughly 3.1418" into the logs
# note you must specify --files argument in cluster mode to enable metrics
spark-submit \
--class org.apache.spark.examples.SparkPi \
--files $SPARK_HOME/conf/metrics.properties \
--master yarn-cluster \
--driver-memory 1g \
--executor-memory 1g \
--executor-cores 1 \
$SPARK_HOME/lib/spark-examples-1.4.0-hadoop2.6.0.jar
```

Estimating Pi (yarn-client mode):

```
# execute the the following command which should print the "Pi is roughly 3.1418" to the screen
spark-submit \
--class org.apache.spark.examples.SparkPi \
--master yarn-client \
--driver-memory 1g \
--executor-memory 1g \
--executor-cores 1 \
$SPARK_HOME/lib/spark-examples-1.4.0-hadoop2.6.0.jar
```
