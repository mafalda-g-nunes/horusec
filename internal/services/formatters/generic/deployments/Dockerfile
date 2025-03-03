# Copyright 2020 ZUP IT SERVICOS EM TECNOLOGIA E INOVACAO SA
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

FROM azul/zulu-openjdk-alpine:18 AS jlink

RUN "$JAVA_HOME/bin/jlink" --compress=2 \
    --module-path /opt/java/openjdk/jmods \
    --add-modules java.base,java.compiler,java.datatransfer,jdk.crypto.ec,java.desktop,java.instrument,java.logging,java.management,java.naming,java.rmi,java.scripting,java.security.sasl,java.sql,java.transaction.xa,java.xml,jdk.unsupported \
    --output /jlinked

FROM python:3.10.4-alpine3.14

# This installation is necessary for semgrep tool dependency: ruamel.yaml.clib>=0.2.6
# See more details in: https://stackoverflow.com/questions/69287269/installing-ruamel-yaml-clib-with-docker
RUN apk add --no-cache gcc musl-dev python3-dev
RUN pip install semgrep==v0.90.0

COPY --from=jlink /jlinked /opt/jdk/

ENV JAVA_HOME=/opt/jdk

RUN apk update && apk add curl

RUN curl -o /bin/dependency-check-7.1.0-release.zip -LO https://github.com/jeremylong/DependencyCheck/releases/download/v7.1.0/dependency-check-7.1.0-release.zip

RUN unzip /bin/dependency-check-7.1.0-release.zip -d  /bin

RUN rm /bin/dependency-check-7.1.0-release.zip

RUN curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh | sh -s -- -b /usr/local/bin v0.27.1
