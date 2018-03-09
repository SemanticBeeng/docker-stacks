# Copyright (c) Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.
FROM jupyter/scipy-notebook

LABEL maintainer="Jupyter Project <jupyter@googlegroups.com>"

# Install Python things we meed from Spark
RUN conda install --quiet --yes \
    'pyarrow=0.8.0*' \
    'rpy2=2.9*' \
    'py4j=0.10.6*' && \
    conda clean -tipsy && \
    fix-permissions $CONDA_DIR && \
    fix-permissions /home/$NB_USER

USER root

# Spark dependencies
ENV APACHE_SPARK_VERSION 2.3.0
ENV HADOOP_VERSION 2.7

RUN apt-get -y update && \
    apt-get install --no-install-recommends -y openjdk-8-jre-headless ca-certificates-java && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
RUN cd /tmp && \
#        wget -q http://d3kbcqa49mib13.cloudfront.net/spark-${APACHE_SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}.tgz && \
        wget -q http://www-eu.apache.org/dist/spark/spark-${APACHE_SPARK_VERSION}/spark-${APACHE_SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}.tgz && \
# #todo     echo "7a186a2a007b2dfd880571f7214a7d329c972510a460a8bdbef9f7f2a891019343c020f74b496a61e5aa42bc9e9a79cc99defe5cb3bf8b6f49c07e01b259bc6b *spark-${APACHE_SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}.tgz" | sha512sum -c - && \
        tar xzf spark-${APACHE_SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}.tgz -C /usr/local && \
        rm spark-${APACHE_SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}.tgz
RUN cd /usr/local && ln -s spark-${APACHE_SPARK_VERSION}-bin-hadoop${HADOOP_VERSION} spark

# Mesos dependencies
RUN . /etc/os-release && \
    apt-key adv --keyserver keyserver.ubuntu.com --recv E56151BF && \
    DISTRO=$ID && \
    CODENAME=$VERSION_CODENAME && \
    echo "deb http://repos.mesosphere.io/${DISTRO} ${CODENAME} main" > /etc/apt/sources.list.d/mesosphere.list && \
    apt-get -y update && \
    apt-get --no-install-recommends -y --force-yes install mesos=1.2\* && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

ENV SPARK_HOME /usr/local/spark
# ADD several jars for S3 connection, mysql...
RUN mkdir -p $SPARK_HOME/jars \
  && wget -P $SPARK_HOME/jars \
    "http://central.maven.org/maven2/mysql/mysql-connector-java/5.1.38/mysql-connector-java-5.1.38.jar"

# Spark and Mesos config
#ENV SPARK_HOME /usr/local/spark
ENV PYTHONPATH $SPARK_HOME/python:$SPARK_HOME/python/lib/py4j-0.10.4-src.zip
ENV MESOS_NATIVE_LIBRARY /usr/local/lib/libmesos.so
ENV SPARK_OPTS --driver-java-options=-Xms1024M --driver-java-options=-Xmx4096M --driver-java-options=-Dlog4j.logLevel=info

USER $NB_UID
