FROM node:8

# make Apt non-interactive
# RUN echo 'APT::Get::Assume-Yes "true";' > /etc/apt/apt.conf.d/90circleci \
#   && echo 'APT::Get::force-Yes "true";' >> /etc/apt/apt.conf.d/90circleci \
#   && echo 'DPkg::Options "--force-confnew";' >> /etc/apt/apt.conf.d/90circleci

ENV DEBIAN_FRONTEND=noninteractive

WORKDIR /tmp

# Install common packages
RUN apt-get update && apt-get install -y --no-install-recommends \
  tar gzip perl locales sudo \
  parallel unzip dh-autoreconf build-essential \
  net-tools netcat zip  xvfb tcl tk \
  ruby python \
  && rm -rf /var/lib/apt/lists/*

# Set timezone to UTC by default
RUN ln -sf /usr/share/zoneinfo/Etc/UTC /etc/localtime

# Use unicode
RUN locale-gen C.UTF-8 || true
ENV LANG=C.UTF-8

# Install Protoc
################
RUN wget https://github.com/google/protobuf/releases/download/v3.5.1/protoc-3.5.1-linux-x86_64.zip \
  && unzip protoc-3.5.1-linux-x86_64.zip \
  && rm protoc-3.5.1-linux-x86_64.zip \
  && chmod +x ./bin/protoc \
  && mv /tmp/bin/protoc /usr/local/bin \
  && mv /tmp/include/google /usr/local/include/

RUN protoc --version

# Use unicode
RUN locale-gen C.UTF-8 || true
ENV LANG=C.UTF-8

# install jq
RUN JQ_URL=$(curl --location --fail --retry 3 https://api.github.com/repos/stedolan/jq/releases/latest  | grep browser_download_url | grep '/jq-linux64"' | grep -o -e 'https.*jq-linux64') \
  && curl --silent --show-error --location --fail --retry 3 --output /usr/bin/jq $JQ_URL \
  && chmod +x /usr/bin/jq

RUN curl https://get.docker.com/ | sh

# docker compose
RUN COMPOSE_URL=$(curl --location --fail --retry 3 https://api.github.com/repos/docker/compose/releases/latest | jq -r '.assets[] | select(.name == "docker-compose-Linux-x86_64") | .browser_download_url') \
  && curl --silent --show-error --location --fail --retry 3 --output /usr/bin/docker-compose $COMPOSE_URL \
  && chmod +x /usr/bin/docker-compose

# install dockerize
RUN DOCKERIZE_URL=$(curl --location --fail --retry 3 https://api.github.com/repos/jwilder/dockerize/releases/latest | jq -r '.assets[] | select(.name | startswith("dockerize-linux-amd64")) | .browser_download_url') \
  && curl --silent --show-error --location --fail --retry 3 --output /tmp/dockerize-linux-amd64.tar.gz $DOCKERIZE_URL \
  && tar -C /usr/local/bin -xzvf /tmp/dockerize-linux-amd64.tar.gz \
  && rm -rf /tmp/dockerize-linux-amd64.tar.gz

WORKDIR /opt

# install gcloud sdk
ENV CLOUDSDK_PYTHON_SITEPACKAGES 1
RUN wget http://dl.google.com/dl/cloudsdk/channels/rapid/google-cloud-sdk.zip && unzip google-cloud-sdk.zip && rm google-cloud-sdk.zip
RUN google-cloud-sdk/install.sh --path-update=true --bash-completion=true --rc-path=/root/.bashrc --additional-components app kubectl beta

RUN sed -i -- 's/\"disable_updater\": false/\"disable_updater\": true/g' /opt/google-cloud-sdk/lib/googlecloudsdk/core/config.json

RUN mkdir ${HOME}/.ssh
ENV PATH /opt/google-cloud-sdk/bin:$PATH