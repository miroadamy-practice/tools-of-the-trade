# 04-04 Building Smaller Images with Multi-Stage Builds

## Introduction

Containers are made of layers. Compile and install operations performed in the image, add to the layers, increasing the size of the container.

Instead of keeping all of those layers into the final image, you can split those steps off, and only use the finished product.

Docker provides this capability through multi-stage builds. In this lab, you will build an image the usual way, and inspect the image to see how it is put together. You'll then convert the Dockerfile to use Multi-Stage builds, and see how the new image compares.

## Solution

Log in to the server using the credentials provided:

`ssh cloud_user@<PUBLIC_IP_ADDRESS>`

### Do Prep Work in the Image

1. Change to the `notes` directory:
    
    `cd notes`
    
2. Check the `Dockerfile`:
    
    `cat Dockerfile`
    
3. Build an image using the file:
    
    `docker build -t notesapp:default .`
    
4. Set a variable to view the layers of the image:
    
    `export showLayers='{{ range .RootFS.Layers }}{{ println . }}{{end}}'`
    
5. Set a variable to show the size of the image:
    
    `export showSize='{{ .Size }}'`
    
6. Show the image layers:
    
    `docker inspect -f "$showLayers" notesapp:default`
    
7. Count the number of layers:
    
    `docker inspect -f "$showLayers" notesapp:default | wc -l`
    
8. Show the size of the image:
    
    `docker inspect -f "$showSize" notesapp:default | numfmt --to=iec`
    

### Add a Build Stage

1. Open the Dockerfile:
    
    `vim Dockerfile`
    
2. Add a build stage by adding the following:

`FROM python:3 AS base
ENV PYBASE /pybase
ENV PYTHONUSERBASE $PYBASE
ENV PATH $PYBASE/bin:$PATH

FROM base AS builder
RUN pip install pipenv
WORKDIR /tmp
COPY Pipfile .
RUN pipenv lock
RUN PIP_USER=1 PIP_IGNORE_INSTALLED=1 pipenv install -d --system --ignore-pipfile

FROM base
COPY --from=builder /pybase /pybase
COPY . /app/notes
WORKDIR /app/notes
EXPOSE 80
CMD [ "flask", "run", "--port=80", "--host=0.0.0.0" ]`

1. Save the file:
    
    `ESC
    :wq`
    

### Create a Smaller Image

1. Build the image:
    
    `docker build -t notesapp:multistage .`
    
2. Show the `multistage` image layers:
    
    `docker inspect -f "$showLayers" notesapp:multistage`
    
3. Count the layers:
    
    `docker inspect -f "$showLayers" notesapp:multistage | wc -l`
    
4. Show the size of the image:
    
    `docker inspect -f "$showSize" notesapp:multistage | numfmt --to=iec`


    # LOG

```sh

cloud_user@ip-10-0-1-116 ~]$ cd notes
[cloud_user@ip-10-0-1-116 notes]$ cat Dockerfile
FROM python:3
ENV PYBASE /pybase
ENV PYTHONUSERBASE $PYBASE
ENV PATH $PYBASE/bin:$PATH

RUN pip install pipenv
WORKDIR /tmp
COPY Pipfile .
RUN pipenv lock
RUN PIP_USER=1 PIP_IGNORE_INSTALLED=1 pipenv install -d --system --ignore-pipfile

COPY . /app/notes
WORKDIR /app/notes
EXPOSE 80
CMD [ "flask", "run", "--port=80", "--host=0.0.0.0" ]


[cloud_user@ip-10-0-1-116 notes]$ docker build -t notesapp:default .
[+] Building 35.5s (13/13) FINISHED
 => [internal] load .dockerignore                                                                                                                        0.0s
 => => transferring context: 2B                                                                                                                          0.0s
 => [internal] load build definition from Dockerfile                                                                                                     0.0s
 => => transferring dockerfile: 433B                                                                                                                     0.0s
 => [internal] load metadata for docker.io/library/python:3                                                                                              0.0s
 => [1/8] FROM docker.io/library/python:3                                                                                                                0.1s
 => [internal] load build context                                                                                                                        0.1s
 => => transferring context: 258.05kB                                                                                                                    0.0s
 => [2/8] RUN pip install pipenv                                                                                                                         9.3s
 => [3/8] WORKDIR /tmp                                                                                                                                   0.0s
 => [4/8] COPY Pipfile .                                                                                                                                 0.0s
 => [5/8] RUN pipenv lock                                                                                                                               16.7s
 => [6/8] RUN PIP_USER=1 PIP_IGNORE_INSTALLED=1 pipenv install -d --system --ignore-pipfile                                                              7.6s
 => [7/8] COPY . /app/notes                                                                                                                              0.0s
 => [8/8] WORKDIR /app/notes                                                                                                                             0.0s
 => exporting to image                                                                                                                                   1.6s
 => => exporting layers                                                                                                                                  1.6s
 => => writing image sha256:f6eddb09242649ddbd4cb9fb528b9a6aedc2f5fe7755e97dd2bd6e713199c287                                                             0.0s
 => => naming to docker.io/library/notesapp:default                                                                                                      0.0s

[cloud_user@ip-10-0-1-116 notes]$ docker images
REPOSITORY   TAG           IMAGE ID       CREATED              SIZE
notesapp     default       f6eddb092426   About a minute ago   1.06GB
python       3             df3e9d105d6c   7 days ago           921MB
postgres     12.1-alpine   76780864f8de   3 years ago          154MB


[cloud_user@ip-10-0-1-116 notes]$ export showLayers='{{ range .RootFS.Layers }}{{ println . }}{{end}}'
[cloud_user@ip-10-0-1-116 notes]$ export showSize='{{ .Size }}'
[cloud_user@ip-10-0-1-116 notes]$ docker inspect -f "$showLayers" notesapp:default
sha256:ec09eb83ea031896df916feb3a61cefba9facf449c8a55d88667927538dca2b4
sha256:053a1f71007ec7293dceb3f229446da4c36bad0d27116ab5324e1c7b61071eae
sha256:a90e3914fb92b189ed9bbe543c4e4cc5be5bd3e7d221ef585405dd35b3e4db43
sha256:5ab567b9150b948de4708a880cad7026665e7e0a227dea526cc3839eca4b2515
sha256:d4514f8b2aac13e66dfc8b6e15aa2feb0d4ff942a192d9481f09b45e681ceb40
sha256:5c563cc8b2164d65f7d80f9d61a7acf805965c54e7f3cb86f2478611351129a5
sha256:13cf5de1dd974bb717b5020966a1fb15f523151d4d95fc3501ebee0c0859245c
sha256:76ff57e73979c903ec8b4da08cb00e611967b49e44d97e9ba44f397d34eed755
sha256:7db0b7dc960a3ef1453a774e4a788cbf00c3e81ddb662cd4d2dfc900ae163195
sha256:2ea1fec3564f27aea6c4c0aefb14763b83f3016a3bb9097f5f087f652ef27568
sha256:5f70bf18a086007016e948b04aed3b82103a36bea41755b6cddfaf10ace3c6ef
sha256:352034408d9cf2ea902db98bff9ec47b88ce63536dfa7f493d01f3e027e05d97
sha256:3941beff9d4067b8c71fda60ed6b845c945bf5c3cfee274e30fa22cbb163402b
sha256:76f1f820416b7b15f8a5030d57405102b5f85be60c707568d2bf79660229fada
sha256:6a280287965b34501e88e487b6aa2a95028a45483441aa2c2a75a0ed96c03bfe
sha256:5f70bf18a086007016e948b04aed3b82103a36bea41755b6cddfaf10ace3c6ef

[cloud_user@ip-10-0-1-116 notes]$ docker inspect -f "$showLayers" notesapp:default | wc -l
17
[cloud_user@ip-10-0-1-116 notes]$
[cloud_user@ip-10-0-1-116 notes]$ docker inspect -f "$showSize" notesapp:default | numfmt --to=iec
1012M


```

## Build stage


```sh
[cloud_user@ip-10-0-1-116 notes]$ vim Dockerfile

[cloud_user@ip-10-0-1-116 notes]$ cat Dockerfile
FROM python:3 AS base
ENV PYBASE /pybase
ENV PYTHONUSERBASE $PYBASE
ENV PATH $PYBASE/bin:$PATH

FROM base AS builder
RUN pip install pipenv
WORKDIR /tmp
COPY Pipfile .
RUN pipenv lock
RUN PIP_USER=1 PIP_IGNORE_INSTALLED=1 pipenv install -d --system --ignore-pipfile

FROM base
COPY --from=builder /pybase /pybase
COPY . /app/notes
WORKDIR /app/notes
EXPOSE 80
CMD [ "flask", "run", "--port=80", "--host=0.0.0.0" ]


[cloud_user@ip-10-0-1-116 notes]$ docker build -t notesapp:multistage .
[+] Building 36.1s (14/14) FINISHED
 => [internal] load build definition from Dockerfile                                                                                                     0.0s
 => => transferring dockerfile: 509B                                                                                                                     0.0s
 => [internal] load .dockerignore                                                                                                                        0.0s
 => => transferring context: 2B                                                                                                                          0.0s
 => [internal] load metadata for docker.io/library/python:3                                                                                              0.0s
 => [internal] load build context                                                                                                                        0.0s
 => => transferring context: 2.60kB                                                                                                                      0.0s
 => CACHED [base 1/1] FROM docker.io/library/python:3                                                                                                    0.0s
 => [builder 1/5] RUN pip install pipenv                                                                                                                 8.9s
 => [builder 2/5] WORKDIR /tmp                                                                                                                           0.0s
 => [builder 3/5] COPY Pipfile .                                                                                                                         0.0s
 => [builder 4/5] RUN pipenv lock                                                                                                                       15.9s
 => [builder 5/5] RUN PIP_USER=1 PIP_IGNORE_INSTALLED=1 pipenv install -d --system --ignore-pipfile                                                      7.8s
 => [stage-2 1/3] COPY --from=builder /pybase /pybase                                                                                                    0.8s
 => [stage-2 2/3] COPY . /app/notes                                                                                                                      0.1s
 => [stage-2 3/3] WORKDIR /app/notes                                                                                                                     0.0s
 => exporting to image                                                                                                                                   0.5s
 => => exporting layers                                                                                                                                  0.5s
 => => writing image sha256:0a0ad9b00e55676a2cf7d15d30cf3cbc8b52d483b96100b7899c3534a637cc29                                                             0.0s
 => => naming to docker.io/library/notesapp:multistage                                                                                                   0.0s
[cloud_user@ip-10-0-1-116 notes]$


[cloud_user@ip-10-0-1-116 notes]$ docker images
REPOSITORY   TAG           IMAGE ID       CREATED              SIZE
notesapp     multistage    0a0ad9b00e55   About a minute ago   959MB
notesapp     default       f6eddb092426   9 minutes ago        1.06GB
python       3             df3e9d105d6c   7 days ago           921MB
postgres     12.1-alpine   76780864f8de   3 years ago          154MB
[cloud_user@ip-10-0-1-116 notes]$ docker inspect -f "$showLayers" notesapp:multistage
sha256:ec09eb83ea031896df916feb3a61cefba9facf449c8a55d88667927538dca2b4
sha256:053a1f71007ec7293dceb3f229446da4c36bad0d27116ab5324e1c7b61071eae
sha256:a90e3914fb92b189ed9bbe543c4e4cc5be5bd3e7d221ef585405dd35b3e4db43
sha256:5ab567b9150b948de4708a880cad7026665e7e0a227dea526cc3839eca4b2515
sha256:d4514f8b2aac13e66dfc8b6e15aa2feb0d4ff942a192d9481f09b45e681ceb40
sha256:5c563cc8b2164d65f7d80f9d61a7acf805965c54e7f3cb86f2478611351129a5
sha256:13cf5de1dd974bb717b5020966a1fb15f523151d4d95fc3501ebee0c0859245c
sha256:76ff57e73979c903ec8b4da08cb00e611967b49e44d97e9ba44f397d34eed755
sha256:7db0b7dc960a3ef1453a774e4a788cbf00c3e81ddb662cd4d2dfc900ae163195
sha256:1938b0e11555cb1c2e6cf1b567bbe9e12e4fe41f0c5b8b30f7d83e997eea6ad3
sha256:8e93c2c54e28b54d685a5ac3908991d816914e691f65c6d06407fc723e20972b
sha256:5f70bf18a086007016e948b04aed3b82103a36bea41755b6cddfaf10ace3c6ef

[cloud_user@ip-10-0-1-116 notes]$ docker inspect -f "$showLayers" notesapp:multistage | wc -l
13
[cloud_user@ip-10-0-1-116 notes]$ docker inspect -f "$showSize" notesapp:multistage | numfmt --to=iec
915M



```