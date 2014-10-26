Docker image for Apache Tez
===========================

This repository contains a docker file to build a docker image with Apache Tez. This docker file is adaptation from [this] repo, except that the docker image for tez runs on top of hadoop 2.5.0 docker base image from my other github repo ([docker-hadoop]).

## Running on Mac OS X

This step is required only for Mac OS X as docker is not natively supported in Mac OS X. To run docker on Mac OS X we need Boot2Docker.

### Setting up docker

* Install Boot2Docker from [here].
* After installing, from terminal, run `boot2docker init` to initialize boot2docker.
* Run `boot2docker start` to start boot2docker and export `DOCKER_HOST` as shown at the end of command. It usually will be `export DOCKER_HOST=tcp://192.168.59.103:2375`
* After exporting `DOCKER_HOST` we can run docker commands.

*NOTE:* docker 1.3.0 versions require --tls to be passed to all docker command

## Pull the image
You can either pull the image that is already pre-built from Docker hub or build the image locally (refer next section)
```
docker --tls pull prasanthj/tez-0.5.0
```

## Building the image

If you do not want to pull the image from Docker hub, you can build it locally using the following steps
* To build the tez docker image locally from Dockerfile, first checkout source using
`git clone https://github.com/prasanthj/docker-tez.git`
* Change to docker-tez directory `cd docker-tez`
```
docker --tls build  -t prasanthj/tez-0.5.0 .
```

## Running the image
```
docker --tls run -i -t -P prasanthj/tez-0.5.0 /etc/bootstrap.sh -bash
```

## Versions
* Apache Hadoop 2.5.0
* Apache Tez 0.5.0

## Testing
When running one of the stock map-reduce examples, the TEZ DAG ApplicationMaster will run the map-reduce job instead of the YARN MR AppMaster.
This can be verified by looking at the YARN ResourceManager UI.
```
$HADOOP_PREFIX/bin/hadoop jar $HADOOP_PREFIX/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.5.0.jar grep input output 'dfs[a-z.]+'
```

There is also a basic Tez MRR job example in one of the tez jars. You can test it by running the following:
```
$HADOOP_PREFIX/bin/hadoop jar /usr/local/tez/tez-examples-0.5.0.jar orderedwordcount input output-owc
```

[here]:https://github.com/boot2docker/osx-installer/releases
[this]:https://github.com/sequenceiq/docker-tez
[docker-hadoop]:https://github.com/prasanthj/docker-hadoop.git