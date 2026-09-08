FROM ubuntu:22.04
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y \
openjdk-8-jdk \
wget \
nano \
vim \
ssh \
rsync \
net-tools

ENV JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
ENV PATH=$JAVA_HOME/bin:$PATH

RUN wget https://archive.apache.org/dist/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz && \
tar -xzf hadoop-3.3.6.tar.gz && \
mv hadoop-3.3.6 /opt/hadoop && \
rm hadoop-3.3.6.tar.gz

ENV HADOOP_HOME=/opt/hadoop
ENV PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
CMD ["/bin/bash"]
