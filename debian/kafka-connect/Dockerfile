#
# Copyright 2016 Confluent Inc.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

# Builds a docker image for Kafka Connect

FROM confluentinc/cp-kafka-connect-base

MAINTAINER partner-support@confluent.io
LABEL io.confluent.docker=true
ARG COMMIT_ID=unknown
LABEL io.confluent.docker.git.id=$COMMIT_ID
ARG BUILD_NUMBER=-1
LABEL io.confluent.docker.build.number=$BUILD_NUMBER

ENV COMPONENT=kafka-connect

RUN echo "===> Installing JDBC, Elasticsearch and Hadoop connectors ..." \
    && apt-get -qq update \
    && apt-get install -y \
        confluent-kafka-connect-elasticsearch=${CONFLUENT_VERSION}${CONFLUENT_PLATFORM_LABEL}-${CONFLUENT_DEB_VERSION} \
    && echo "===> Cleaning up ..."  \
    && apt-get clean && rm -rf /tmp/* /var/lib/apt/lists/*

RUN echo "===> Installing GCS Sink Connector ..."
RUN confluent-hub install confluentinc/kafka-connect-gcs:latest --no-prompt
RUN echo "===> Installing Bigquery Sink Connector ..."
RUN confluent-hub install wepay/kafka-connect-bigquery:1.1.0 --no-prompt


COPY docker-maven-download.sh /usr/local/bin/docker-maven-download
RUN chmod a+x /usr/local/bin/docker-maven-download

ENV KAFKA_HOME=/etc/kafka
ENV KAFKA_CONNECT_PLUGINS_DIR=$KAFKA_HOME/connect \
    CONNECT_PLUGIN_PATH=$KAFKA_CONNECT_PLUGINS_DIR \
    MAVEN_DEP_DESTINATION=$KAFKA_HOME/libs \
    CONFLUENT_VERSION=5.2.2 \
    AVRO_VERSION=1.8.2 \
    AVRO_JACKSON_VERSION=1.9.13

ENV DEBEZIUM_VERSION="0.9.5.Final" \
    MAVEN_REPO_CORE="https://repo1.maven.org/maven2" \
    MAVEN_DEP_DESTINATION=$KAFKA_CONNECT_PLUGINS_DIR \
    MYSQL_MD5=720b1396358fbdc59bce953f47d3c53f

RUN mkdir $KAFKA_CONNECT_PLUGINS_DIR;

RUN docker-maven-download confluent kafka-connect-avro-converter "$CONFLUENT_VERSION" a1439cc44bea69077061182bd197d7f1 && \
    docker-maven-download confluent kafka-avro-serializer "$CONFLUENT_VERSION" c39c20024baab5ffdf72dbab09e9dc30 && \
    docker-maven-download confluent kafka-schema-registry-client "$CONFLUENT_VERSION" 690304aade62e0b86fde1e58f9e5af63 && \
    docker-maven-download confluent common-config "$CONFLUENT_VERSION" 06a821c83cff89a5413f375598ccae55 && \
    docker-maven-download confluent common-utils "$CONFLUENT_VERSION" 542c68e03afedd4880aa708936aacb36 && \
    docker-maven-download central org/codehaus/jackson jackson-core-asl "$AVRO_JACKSON_VERSION" 319c49a4304e3fa9fe3cd8dcfc009d37 && \
    docker-maven-download central org/codehaus/jackson jackson-mapper-asl "$AVRO_JACKSON_VERSION" 1750f9c339352fc4b728d61b57171613 && \
    docker-maven-download central org/apache/avro avro "$AVRO_VERSION" 10395e5a571e1a1f6113411f276d2fea


RUN docker-maven-download debezium mysql "$DEBEZIUM_VERSION" "$MYSQL_MD5"

# Install pubsub
RUN wget --quiet https://www-us.apache.org/dist/maven/maven-3/3.6.0/binaries/apache-maven-3.6.0-bin.tar.gz -P /tmp && \
    tar xf /tmp/apache-maven-*.tar.gz -C /opt && \
    rm /tmp/apache-maven-*.tar.gz && \
    ln -s /opt/apache-maven-3.6.0 /opt/maven && \
    curl --silent -L -o pubsub.tar.gz https://github.com//GoogleCloudPlatform/pubsub/archive/v0.3.tar.gz && \
    tar -xf pubsub.tar.gz && \
    rm pubsub.tar.gz && \
    cd pubsub-0.3/kafka-connector/ && \
    /opt/maven/bin/mvn package && \
    cp target/cps-kafka-connector.jar $KAFKA_CONNECT_PLUGINS_DIR/cps-kafka-connector.jar && \
    rm -r ../../pubsub-0.3 && \
    rm -r /opt/apache-maven-3.6.0 && \
    rm /opt/maven
