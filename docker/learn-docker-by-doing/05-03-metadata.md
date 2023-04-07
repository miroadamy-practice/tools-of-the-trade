# Adding Metadata and Labels

The lab 'Adding Metadata and Labels' does not start. 

It hangs in Finalizing .…

... EVENTUALLY

```sh

[cloud_user@docker-workstation ~]$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
[cloud_user@docker-workstation ~]$ docker image ls
REPOSITORY   TAG       IMAGE ID   CREATED   SIZE
[cloud_user@docker-workstation ~]$ docker images
REPOSITORY   TAG       IMAGE ID   CREATED   SIZE
[cloud_user@docker-workstation ~]$


vim Dockerfile

FROM node

LABEL maintainer="miro.adamy@gmail.com"

ARG BUILD_VERSION
ARG BUILD_DATE
ARG APPLICATION_NAME

LABEL org.label-schema.build-date=$BUILD_DATE
LABEL org.label-schema.application=$APPLICATION_NAME
LABEL org.label-schema.version=$BUILD_VERSION

RUN mkdir -p /var/node
ADD weather-app/ /var/node/
WORKDIR /var/node
RUN npm install
EXPOSE 3000
CMD ./bin/www


docker build -t miroadamy/weather-app --build-arg BUILD_DATE=$(date -u +'%Y-%m-%dT%H:%M:%SZ') \
--build-arg APPLICATION_NAME=weather-app --build-arg BUILD_VERSION=v1.0 -f Dockerfile


[cloud_user@docker-workstation ~]$ docker build -t miroadamy/weather-app --build-arg BUILD_DATE=$(date -u +'%Y-%m-%dT%H:%M:%SZ') --build-arg APPLICATION_NAME=weather-app --build-arg BUILD_VERSION=v1.0 -f Dockerfile .
Sending build context to Docker daemon   12.8kB
Step 1/14 : FROM node
latest: Pulling from library/node
3e440a704568: Pull complete
68a71c865a2c: Pull complete
670730c27c2e: Pull complete
5a7a2c95f0f8: Pull complete
6d627e120214: Pull complete
1dc4abe482bf: Pull complete
5380f3e67b53: Pull complete
4b3618a94e24: Pull complete
ff966bd0f86f: Pull complete
Digest: sha256:1fab548e95c779df229e4b50d8d20e222597bda15aeece508098c5ba7723302e
Status: Downloaded newer image for node:latest
 ---> 0e0ab07dbedd
Step 2/14 : LABEL maintainer="miro.adamy@gmail.com"
 ---> Running in 57b5925457ca
Removing intermediate container 57b5925457ca
 ---> 34f0105e96c4
Step 3/14 : ARG BUILD_VERSION
 ---> Running in 32ece1812460
Removing intermediate container 32ece1812460
 ---> ff8a4fe8a253
Step 4/14 : ARG BUILD_DATE
 ---> Running in f7f8e45fa45c
Removing intermediate container f7f8e45fa45c
 ---> 3a592fe7a140
Step 5/14 : ARG APPLICATION_NAME
 ---> Running in 075bb86789d2
Removing intermediate container 075bb86789d2
 ---> 0994ef448300
Step 6/14 : LABEL org.label-schema.build-date=$BUILD_DATE
 ---> Running in 4b09fa16df61
Removing intermediate container 4b09fa16df61
 ---> 585c2617c473
Step 7/14 : LABEL org.label-schema.application=$APPLICATION_NAME
 ---> Running in aea3c36d1ea1
Removing intermediate container aea3c36d1ea1
 ---> 37bef6aa6d12
Step 8/14 : LABEL org.label-schema.version=$BUILD_VERSION
 ---> Running in 8bcc668cdaa5
Removing intermediate container 8bcc668cdaa5
 ---> 0bd593de405a
Step 9/14 : RUN mkdir -p /var/node
 ---> Running in 7b14bd144ae5
Removing intermediate container 7b14bd144ae5
 ---> 7b4398a9bd9f
Step 10/14 : ADD weather-app/ /var/node/
ADD failed: file not found in build context or excluded by .dockerignore: stat weather-app/: file does not exist
```

After it starts, it does not contain waether-app