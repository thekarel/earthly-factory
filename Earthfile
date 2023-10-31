VERSION 0.7
FROM node:18-alpine
WORKDIR /factory

main:
  FROM +factory
  WORKDIR /main

  FROM DOCKERFILE -f +dist/Dockerfile .

  SAVE IMAGE "x:latest"

  BUILD +helm \
    --STAGE=dev

dist:
  COPY (+get-thing/ --THING=xyz) ./

  SAVE ARTIFACT ./thing/Dockerfile /

get-thing:
  FROM +factory
  ARG --required THING

  SAVE ARTIFACT ./Dockerfile /thing/Dockerfile

helm:
  FROM +factory
  ARG --required STAGE

  RUN helm version
  RUN --push helm env

factory:
  RUN apk add curl openssl python3 make g++

  RUN wget -q -t3 'https://packages.doppler.com/public/cli/rsa.8004D9FF50437357.key' -O /etc/apk/keys/cli@doppler-8004D9FF50437357.rsa.pub && \
      echo 'https://packages.doppler.com/public/cli/alpine/any-version/main' | tee -a /etc/apk/repositories && \
      apk add doppler

  RUN curl -fsSL https://get.pulumi.com | sh && \
      cp ~/.pulumi/bin/* /usr/local/bin/

  RUN corepack enable
  RUN corepack prepare pnpm@latest --activate

  RUN npm install -g zx

  RUN curl -O https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-cli-451.0.1-linux-x86_64.tar.gz
  RUN tar -xf google-cloud-cli-451.0.1-linux-x86_64.tar.gz
  RUN ./google-cloud-sdk/install.sh
  ENV PATH="./google-cloud-sdk/bin:${PATH}"
  RUN gcloud components install kubectl

  RUN curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
  RUN chmod 700 get_helm.sh
  RUN sh ./get_helm.sh

  COPY Dockerfile .
  COPY package.json .
