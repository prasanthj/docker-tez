FROM prasanthj/docker-hadoop:2.6.0
MAINTAINER Prasanth Jayachandran

# dev tools to build tez
RUN apt-get update
RUN apt-get install -y git libprotobuf-dev protobuf-compiler npm

# to run bower as root
RUN echo '{ "allow_root": true }' > /root/.bowerrc

# install maven
RUN curl -s http://mirror.olnevhost.net/pub/apache/maven/binaries/apache-maven-3.2.1-bin.tar.gz | tar -xz -C /usr/local/
RUN cd /usr/local && ln -s apache-maven-3.2.1 maven
ENV MAVEN_HOME /usr/local/maven
ENV PATH $MAVEN_HOME/bin:$PATH

# download tez code, switch to 0.8.4 branch, compile and copy jars
ENV TEZ_VERSION 0.8.4
ENV TEZ_DIST /usr/local/tez/tez-dist/target/tez-${TEZ_VERSION}
RUN cd /usr/local && git clone https://github.com/apache/tez.git
RUN cd /usr/local/tez && git checkout tags/rel/release-0.8.4 -b branch-0.8 && mvn clean package -DskipTests=true -Dmaven.javadoc.skip=true
RUN $BOOTSTRAP && $HADOOP_PREFIX/bin/hadoop dfsadmin -safemode leave && $HADOOP_PREFIX/bin/hdfs dfs -put ${TEZ_DIST} /tez

# prepare tez ui
RUN mkdir /var/www/tez-ui && cd /var/www/tez-ui && jar -xvf ${TEZ_DIST}/tez-ui2-${TEZ_VERSION}.war
RUN service apache2 restart

# add site files
ADD tez-site.xml $HADOOP_PREFIX/etc/hadoop/tez-site.xml
ADD mapred-site.xml $HADOOP_PREFIX/etc/hadoop/mapred-site.xml

# environment settings
RUN echo 'TEZ_JARS=${TEZ_DIST}/*' >> $HADOOP_PREFIX/etc/hadoop/hadoop-env.sh
RUN echo 'TEZ_LIB=${TEZ_DIST}/lib/*' >> $HADOOP_PREFIX/etc/hadoop/hadoop-env.sh
RUN echo 'TEZ_CONF=/usr/local/hadoop/etc/hadoop' >> $HADOOP_PREFIX/etc/hadoop/hadoop-env.sh
RUN echo 'export HADOOP_CLASSPATH=$HADOOP_CLASSPATH:$TEZ_CONF:$TEZ_JARS:$TEZ_LIB' >> $HADOOP_PREFIX/etc/hadoop/hadoop-env.sh

# execute hadoop bootstrap script
CMD ["/etc/bootstrap.sh", "-d"]