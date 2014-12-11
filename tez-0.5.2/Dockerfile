FROM prasanthj/docker-hadoop
MAINTAINER Prasanth Jayachandran

# dev tools to build tez
RUN apt-get update
RUN apt-get install -y git libprotobuf-dev protobuf-compiler

# install maven
RUN curl -s http://mirror.olnevhost.net/pub/apache/maven/binaries/apache-maven-3.2.1-bin.tar.gz | tar -xz -C /usr/local/
RUN cd /usr/local && ln -s apache-maven-3.2.1 maven
ENV MAVEN_HOME /usr/local/maven
ENV PATH $MAVEN_HOME/bin:$PATH

# download tez code, switch to 0.5 branch, compile and copy jars
ENV TEZ_VERSION 0.5.2
ENV TEZ_DIST /usr/local/tez/tez-dist/target/tez-${TEZ_VERSION}
RUN cd /usr/local && git clone https://github.com/apache/tez.git -b branch-0.5.2
RUN cd /usr/local/tez && mvn clean package -DskipTests=true -Dmaven.javadoc.skip=true
RUN $BOOTSTRAP && $HADOOP_PREFIX/bin/hadoop dfsadmin -safemode leave && $HADOOP_PREFIX/bin/hdfs dfs -put ${TEZ_DIST} /tez

# add tez specific configs
ADD tez-site.xml $HADOOP_PREFIX/etc/hadoop/tez-site.xml
ADD mapred-site.xml $HADOOP_PREFIX/etc/hadoop/mapred-site.xml

# environment settings
RUN echo 'TEZ_JARS=${TEZ_DIST}/*' >> $HADOOP_PREFIX/etc/hadoop/hadoop-env.sh
RUN echo 'TEZ_LIB=${TEZ_DIST}/lib/*' >> $HADOOP_PREFIX/etc/hadoop/hadoop-env.sh
RUN echo 'TEZ_CONF=/usr/local/hadoop/etc/hadoop' >> $HADOOP_PREFIX/etc/hadoop/hadoop-env.sh
RUN echo 'export HADOOP_CLASSPATH=$HADOOP_CLASSPATH:$TEZ_CONF:$TEZ_JARS:$TEZ_LIB' >> $HADOOP_PREFIX/etc/hadoop/hadoop-env.sh

# execute hadoop bootstrap script
CMD ["/etc/bootstrap.sh", "-d"]
