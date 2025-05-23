FROM ubuntu:20.04
RUN rm /bin/sh && ln -s /bin/bash /bin/sh

USER root

RUN apt-get update

# Set Timezone
RUN DEBIAN_FRONTEND="noninteractive" apt-get -y install tcl && echo "Asia/Taipei" > /etc/timezone

# First, set timezone, and then install java
RUN apt-get install -y wget openssh-server openjdk-8-jdk

# Set SSH passwordless login
RUN ssh-keygen -t rsa -f ~/.ssh/id_rsa -P '' && cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
RUN sed -i 's/#   StrictHostKeyChecking ask/StrictHostKeyChecking accept-new/' /etc/ssh/ssh_config

# Download  Hadoop
RUN cd /usr/local && wget -O ./hadoop.tar.gz https://archive.apache.org/dist/hadoop/common/hadoop-3.3.1/hadoop-3.3.1.tar.gz && tar -xvf hadoop.tar.gz && chown root: -R /usr/local/hadoop-3.3.1 && mv hadoop-3.3.1 hadoop
# Download  Hive
RUN cd /usr/local && wget -O ./hive.tar.gz https://archive.apache.org/dist/hive/hive-2.3.9/apache-hive-2.3.9-bin.tar.gz && tar -xvf hive.tar.gz && chown root: -R /usr/local/apache-hive-2.3.9-bin && mv apache-hive-2.3.9-bin hive

RUN echo -e $'# JAVA set environment variables\n\
JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64\n\
# Set HADOOP_HOME\n\
HADOOP_HOME=/usr/local/hadoop\n\
# Set HADOOP_MAPRED_HOME\n\
HADOOP_MAPRED_HOME=${HADOOP_HOME}\n\
# Add Hadoop bin and sbin directory to PATH\n\
PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin\n\
# Add HIVE_HOME
export HIVE_HOME=/usr/local/hive\n\
# Add HIVE_HOME path
export PATH=$PATH:$HIVE_HOME/bin' >> /root/.bashrc && source /root/.bashrc

# Set hadoop-env.sh
RUN sed -i '55s@.*@export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64@g' /usr/local/hadoop/etc/hadoop/hadoop-env.sh
RUN sed -i '59s@.*@export HADOOP_HOME=/usr/local/hadoop@g' /usr/local/hadoop/etc/hadoop/hadoop-env.sh
RUN sed -i '69s@.*@export HADOOP_CONF_DIR=/usr/local/hadoop/etc/hadoop@g' /usr/local/hadoop/etc/hadoop/hadoop-env.sh

# Set $HIVE_HOME/bin/hive-config.sh
RUN echo -e $'export HADOOP_HOME=/usr/local/hadoop' >> /usr/local/hive/bin/hive-config.sh

# Set User in hadoop-env.sh
RUN sed -i '319s@.*@export HDFS_DATANODE_SECURE_USER=root@g' /usr/local/hadoop/etc/hadoop/hadoop-env.sh
RUN sed -i '348s@.*@export HDFS_NFS3_SECURE_USER=root@g' /usr/local/hadoop/etc/hadoop/hadoop-env.sh
RUN sed -i '418s@.*@export HDFS_NAMENODE_USER=root@g' /usr/local/hadoop/etc/hadoop/hadoop-env.sh
RUN sed -i '426s@.*@export HADOOP_REGISTRYDNS_SECURE_USER=root@g' /usr/local/hadoop/etc/hadoop/hadoop-env.sh
RUN echo -e $'export HDFS_SECONDARYNAMENODE_USER=root' >> /usr/local/hadoop/etc/hadoop/hadoop-env.sh
RUN echo -e $'export HADOOP_SHELL_EXECNAME=root' >> /usr/local/hadoop/etc/hadoop/hadoop-env.sh
RUN echo -e $'export YARN_RESOURCEMANAGER_USER=root' >> /usr/local/hadoop/etc/hadoop/hadoop-env.sh
RUN echo -e $'export YARN_NODEMANAGER_USER=root' >> /usr/local/hadoop/etc/hadoop/hadoop-env.sh
RUN echo -e $'export HDFS_JOURNALNODE_USER=root' >> /usr/local/hadoop/etc/hadoop/hadoop-env.sh
RUN echo -e $'export HDFS_ZKFC_USER=root' >> /usr/local/hadoop/etc/hadoop/hadoop-env.sh

# Set workers overrides original workers
RUN echo -e $'worker1\n\
worker2\n\
worker3' > /usr/local/hadoop/etc/hadoop/workers

# Set core-site.xml, mapred-site.xml, yarn-site.xml, hdfs-site.xml
COPY ./core-site.xml /usr/local/hadoop/etc/hadoop/core-site.xml
COPY ./mapred-site.xml /usr/local/hadoop/etc/hadoop/mapred-site.xml
COPY ./yarn-site.xml /usr/local/hadoop/etc/hadoop/yarn-site.xml

# Set Hive WebUI
COPY ./hdfs-site.xml /usr/local/hadoop/etc/hadoop/hdfs-site.xml

# Set hive-site
COPY ./hive-site.xml /usr/local/hive/conf/hive-site.xml

# Start start-dfs.sh
RUN echo -e $'export HADOOP_OPTS="$HADOOP_OPTS -Djava.net.preferIPv4Stack=true -Djava.security.krb5.realm= -Djava.security.krb5.kdc="' >> /usr/local/hadoop/etc/hadoop/hadoop-env.sh

# Set file for NN INFO
RUN mkdir -p /usr/local/hadoop/data/dfs/name /usr/local/hadoop/data/journalnode /usr/local/hadoop/data/namenode /usr/local/hadoop/data/datanode

# Remove downloaded files
RUN rm /usr/local/hadoop.tar.gz
RUN rm /usr/local/hive.tar.gz

# Set file for Hive system:java.io.tmpdir
RUN mkdir -p /usr/local/hive/tmp

# Solve Hive multiple the same files
RUN rm /usr/local/hive/lib/log4j-slf4j-impl-2.6.2.jar
