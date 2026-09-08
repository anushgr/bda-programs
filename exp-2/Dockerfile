FROM eclipse-temurin:8-jdk

RUN apt-get update && \
    apt-get install -y wget && \
    wget https://archive.apache.org/dist/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz && \
    tar -xzf hadoop-3.3.6.tar.gz -C /opt && \
    rm hadoop-3.3.6.tar.gz

ENV JAVA_HOME=/opt/java/openjdk
ENV HADOOP_HOME=/opt/hadoop-3.3.6
ENV HADOOP_CONF_DIR=/opt/hadoop-3.3.6/etc/hadoop
ENV PATH=$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$JAVA_HOME/bin:$PATH

RUN mkdir -p /tmp/hadoop-root/dfs/name /tmp/hadoop-root/dfs/data

WORKDIR /workspace