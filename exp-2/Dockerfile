FROM apache/hadoop:3.3.6

USER root

COPY --from=eclipse-temurin:8-jdk /opt/java/openjdk /opt/java/openjdk

ENV JAVA_HOME=/opt/java/openjdk
ENV PATH=$JAVA_HOME/bin:$PATH

USER root