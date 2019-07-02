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

FROM jinoo/cp-kafka-connect-base

MAINTAINER partner-support@confluent.io
LABEL io.confluent.docker=true
ARG COMMIT_ID=unknown
LABEL io.confluent.docker.git.id=$COMMIT_ID
ARG BUILD_NUMBER=-1
LABEL io.confluent.docker.build.number=$BUILD_NUMBER

COPY docker-maven-download.sh /usr/local/bin/docker-maven-download
RUN chmod a+x /usr/local/bin/docker-maven-download

#
# Set up the plugins directory ...
#
ENV KAFKA_HOME=/etc/kafka
ENV KAFKA_CONNECT_PLUGINS_DIR=$KAFKA_HOME/connect \
    CONNECT_PLUGIN_PATH=$KAFKA_CONNECT_PLUGINS_DIR \
    MAVEN_DEP_DESTINATION=$KAFKA_HOME/libs \
    CONFLUENT_VERSION=5.2.2 \
    AVRO_VERSION=1.8.2 \
    AVRO_JACKSON_VERSION=1.9.13

ENV COMPONENT=kafka-connect

RUN echo "===> Installing GCS Sink Connector ..."
RUN confluent-hub install confluentinc/kafka-connect-gcs:latest --no-prompt
RUN echo "===> Installing Bigquery Sink Connector ..."
RUN confluent-hub install wepay/kafka-connect-bigquery:1.1.0 --no-prompt
#RUN confluent-hub install confluentinc/kafka-connect-jdbc:5.2.2 --no-prompt
#RUN apt-get install -y wget \
    #&& wget https://repo1.maven.org/maven2/mysql/mysql-connector-java/8.0.16/mysql-connector-java-8.0.16.jar -P /usr/share/confluent-hub-components/confluentinc-kafka-connect-jdbc

ENV DEBEZIUM_VERSION="0.9.5.Final" \
    MAVEN_REPO_CORE="https://repo1.maven.org/maven2" \
    MAVEN_DEP_DESTINATION=$KAFKA_CONNECT_PLUGINS_DIR \
    MYSQL_MD5=720b1396358fbdc59bce953f47d3c53f

RUN mkdir $KAFKA_CONNECT_PLUGINS_DIR;

#RUN docker-maven-download confluent kafka-connect-avro-converter "$CONFLUENT_VERSION" b6b8507bdc518d8c3d7d4a62b6140977 && \
    #docker-maven-download confluent kafka-avro-serializer "$CONFLUENT_VERSION" 464857f51204412996e2bb81d67a94e8 && \
    #docker-maven-download confluent kafka-schema-registry-client "$CONFLUENT_VERSION" e3078fa805dd793e9d97d2bf395d037a && \
    #docker-maven-download confluent common-config "$CONFLUENT_VERSION" 4ee0fb996cea8691196c4187876b388b && \
    #docker-maven-download confluent common-utils "$CONFLUENT_VERSION" 7aef21c3824f218252b8f2383c71840f && \
RUN docker-maven-download confluent kafka-connect-avro-converter "$CONFLUENT_VERSION" a1439cc44bea69077061182bd197d7f1 && \
    docker-maven-download confluent kafka-avro-serializer "$CONFLUENT_VERSION" c39c20024baab5ffdf72dbab09e9dc30 && \
    docker-maven-download confluent kafka-schema-registry-client "$CONFLUENT_VERSION" 690304aade62e0b86fde1e58f9e5af63 && \
    docker-maven-download confluent common-config "$CONFLUENT_VERSION" 06a821c83cff89a5413f375598ccae55 && \
    docker-maven-download confluent common-utils "$CONFLUENT_VERSION" 542c68e03afedd4880aa708936aacb36 && \
    docker-maven-download central org/codehaus/jackson jackson-core-asl "$AVRO_JACKSON_VERSION" 319c49a4304e3fa9fe3cd8dcfc009d37 && \
    docker-maven-download central org/codehaus/jackson jackson-mapper-asl "$AVRO_JACKSON_VERSION" 1750f9c339352fc4b728d61b57171613 && \
    docker-maven-download central org/apache/avro avro "$AVRO_VERSION" 10395e5a571e1a1f6113411f276d2fea


RUN docker-maven-download debezium mysql "$DEBEZIUM_VERSION" "$MYSQL_MD5"
