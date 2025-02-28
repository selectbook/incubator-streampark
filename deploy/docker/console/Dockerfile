###############################################################################
#  Licensed to the Apache Software Foundation (ASF) under one or more
#  contributor license agreements.  See the NOTICE file distributed with
#  this work for additional information regarding copyright ownership.
#  The ASF licenses this file to You under the Apache License, Version 2.0
#  (the "License"); you may not use this file except in compliance with
#  the License.  You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
#  Unless required by applicable law or agreed to in writing, software
#  distributed under the License is distributed on an "AS IS" BASIS,
#  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
#  See the License for the specific language governing permissions and
#  limitations under the License.
###############################################################################
FROM alpine:3.16 as deps-stage
COPY . /
WORKDIR /
RUN tar zxvf streampark-console/streampark-console-service/target/streampark-console-service-*.tar.gz \
&& mv streampark-console-service-* streampark


FROM docker:dind
WORKDIR /streampark
COPY --from=deps-stage /streampark /streampark

ARG DB=localhost
ENV NODE_VERSION=16.1.0
ENV NPM_VERSION=7.11.2

RUN sed -i -e 's/eval $NOHUP/eval/' bin/streampark.sh \
    && sed -i -e 's/StreamParkConsoleBootstrap \\/StreamParkConsoleBootstrap/' bin/streampark.sh \
    && sed -i -e 's/>> "$APP_OUT" 2>&1 "&"//' bin/streampark.sh \
    && sed -i -e 's/localhost/'$DB'/' conf/application.yml

RUN apk add openjdk8 \
    && apk add maven \
    && apk add wget \
    && apk add vim \
    && apk add bash

ENV JAVA_HOME=/usr/lib/jvm/java-1.8-openjdk
ENV MAVEN_HOME=/usr/share/java/maven-3
ENV PATH $JAVA_HOME/bin:$PATH
ENV PATH $MAVEN_HOME/bin:$PATH



RUN wget "https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-x64.tar.gz" \
    && tar zxvf "node-v$NODE_VERSION-linux-x64.tar.gz" -C /usr/local --strip-components=1 \
    && rm "node-v$NODE_VERSION-linux-x64.tar.gz" \
    && ln -s /usr/local/bin/node /usr/local/bin/nodejs

RUN mkdir -p ~/.kube \
    && cat plugins/config >> ~/.kube/config

EXPOSE 10000
