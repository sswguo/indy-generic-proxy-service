FROM quay.io/factory2/spmm-pipeline-base:latest AS builder

ARG GIT_REVISION=main

RUN mkdir workspace && \
    cd workspace && \
    git init && \
    git clone -b ${GIT_REVISION} --depth 1 https://github.com/Commonjava/indy-generic-proxy-service.git

RUN cd workspace/indy-generic-proxy-service && \
    mvn package -Dquarkus.package.type=uber-jar

FROM registry.access.redhat.com/ubi9/openjdk-11-runtime:1.21-1

USER root

ENV JAVA_HOME=/usr/lib/jvm/jre-11-openjdk
ENV JAVA_CMD=$JAVA_HOME/bin/java

RUN mkdir -p /deployment/log /deployment/config && \
  chmod -R 777 /deployment/log /deployment/config

COPY --from=builder /workspace/indy-generic-proxy-service/target/*-runner.jar /deployment/indy-generic-proxy-service-runner.jar
RUN chmod +r /deployment/indy-generic-proxy-service-runner.jar

USER 1001

WORKDIR /

ENTRYPOINT ["bash", "-c"]
CMD ["$JAVA_CMD","$JAVA_OPTS","-jar","/deployment/indy-generic-proxy-service-runner.jar"]