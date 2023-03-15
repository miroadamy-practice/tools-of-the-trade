# Working With Prebuilt Docker Images

## Introduction

After installation, the best way to familiarize yourself with Docker, is to run containers from a few prebuilt images.

In this lab, you will explore Docker Hub for images that will run a website. Once you find suitable images, you will get them into your development environment and begin experimenting. You will run, stop, and delete containers from those images. You will also learn how to use existing data in containers.

```sh
[cloud_user@ip-10-0-1-193 ~]$ docker images
REPOSITORY   TAG       IMAGE ID   CREATED   SIZE
[cloud_user@ip-10-0-1-193 ~]$ docker pull httpd:2.4
2.4: Pulling from library/httpd
3f9582a2cbe7: Pull complete
9423d69c3be7: Pull complete
d1f584c02b5d: Pull complete
758a20a64707: Pull complete
08507f82f391: Pull complete
Digest: sha256:76618ddd53f315a1436a56dc84ad57032e1b2123f2f6489ce9c575c4b280c4f4
Status: Downloaded newer image for httpd:2.4
docker.io/library/httpd:2.4

# This does not work
[cloud_user@ip-10-0-1-193 ~]$ docker images ls
REPOSITORY   TAG       IMAGE ID   CREATED   SIZE

# Old format
[cloud_user@ip-10-0-1-193 ~]$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED      SIZE
httpd        2.4       daab1fa13f86   8 days ago   145MB

# NEw format
[cloud_user@ip-10-0-1-193 ~]$ docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED      SIZE
httpd        2.4       daab1fa13f86   8 days ago   145MB
```

Run

```sh
[cloud_user@ip-10-0-1-193 ~]$ docker run --name httpd -p 8080:80 -d httpd:2.4
8f213b2d06987bf868a7ede1900ef0a641e7f144aecb3ce7d971e46014968112

[cloud_user@ip-10-0-1-193 ~]$ docker ps
CONTAINER ID   IMAGE       COMMAND              CREATED         STATUS         PORTS                                   NAMES
8f213b2d0698   httpd:2.4   "httpd-foreground"   7 seconds ago   Up 6 seconds   0.0.0.0:8080->80/tcp, :::8080->80/tcp   httpd

http://54.159.18.7:8080/ => it works

```

Get widget factory up and running


```sh
[cloud_user@ip-10-0-1-193 ~]$ git clone https://github.com/linuxacademy/content-widget-factory-inc
Cloning into 'content-widget-factory-inc'...
remote: Enumerating objects: 11, done.
remote: Counting objects: 100% (11/11), done.
remote: Compressing objects: 100% (7/7), done.
remote: Total 11 (delta 3), reused 11 (delta 3), pack-reused 0
Unpacking objects: 100% (11/11), done.

[cloud_user@ip-10-0-1-193 ~]$ cd content-widget-factory-inc/

[cloud_user@ip-10-0-1-193 content-widget-factory-inc]$ ll
total 0
drwxrwxr-x. 3 cloud_user cloud_user 93 Mar 15 19:09 web

[cloud_user@ip-10-0-1-193 content-widget-factory-inc]$ cd web/

[cloud_user@ip-10-0-1-193 web]$ ll
total 16
drwxrwxr-x. 2 cloud_user cloud_user   76 Mar 15 19:09 img
-rw-rw-r--. 1 cloud_user cloud_user 3059 Mar 15 19:09 index.html
-rw-rw-r--. 1 cloud_user cloud_user 2910 Mar 15 19:09 quote.html
-rw-rw-r--. 1 cloud_user cloud_user 2611 Mar 15 19:09 support.html
-rw-rw-r--. 1 cloud_user cloud_user 2645 Mar 15 19:09 widgets.html

[cloud_user@ip-10-0-1-193 web]$ docker stop httpd
httpd

[cloud_user@ip-10-0-1-193 web]$ docker rm httpd
httpd

[cloud_user@ip-10-0-1-193 web]$ docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

[cloud_user@ip-10-0-1-193 web]$ docker run --name httpd -p 8080:80 -v $(pwd):/usr/local/apache2/htdocs:ro -d httpd:2.4
54e33277ea3f57033c496eb0b9786a0b35571e522e3a639ed338cf6d198215fe

```

## Same thing with Nginx

```sh

[cloud_user@ip-10-0-1-193 web]$ docker pull nginx
Using default tag: latest
latest: Pulling from library/nginx
3f9582a2cbe7: Already exists
9a8c6f286718: Pull complete
e81b85700bc2: Pull complete
73ae4d451120: Pull complete
6058e3569a68: Pull complete
3a1b8f201356: Pull complete
Digest: sha256:aa0afebbb3cfa473099a62c4b32e9b3fb73ed23f2a75a65ce1d4b4f55a5c2ef2
Status: Downloaded newer image for nginx:latest
docker.io/library/nginx:latest

[cloud_user@ip-10-0-1-193 web]$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
httpd        2.4       daab1fa13f86   8 days ago    145MB
nginx        latest    904b8cb13b93   2 weeks ago   142MB

[cloud_user@ip-10-0-1-193 web]$ docker run --name nginx -p 8081:80 -d nginx
64b7be50290cbf3df3bc947506f3d37580cf7d8453cb9c058be3751c5241a2a8

[cloud_user@ip-10-0-1-193 web]$ docker ps
CONTAINER ID   IMAGE       COMMAND                  CREATED         STATUS         PORTS                                   NAMES
64b7be50290c   nginx       "/docker-entrypoint.…"   5 seconds ago   Up 4 seconds   0.0.0.0:8081->80/tcp, :::8081->80/tcp   nginx
54e33277ea3f   httpd:2.4   "httpd-foreground"       2 minutes ago   Up 2 minutes   0.0.0.0:8080->80/tcp, :::8080->80/tcp   httpd

[cloud_user@ip-10-0-1-193 web]$ docker stop nginx
docker rm nginx
nginx

[cloud_user@ip-10-0-1-193 web]$ docker rm nginx
nginx

[cloud_user@ip-10-0-1-193 web]$ docker run --name nginx -v $(pwd):/usr/share/nginx/html:ro -p 8081:80 -d nginx
9b3c499f228d25a8ca5500b7a7c3e43eddce5c1d378ab59c5ea79f0650151670

[cloud_user@ip-10-0-1-193 web]$ docker ps
CONTAINER ID   IMAGE       COMMAND                  CREATED         STATUS         PORTS                                   NAMES
9b3c499f228d   nginx       "/docker-entrypoint.…"   5 seconds ago   Up 4 seconds   0.0.0.0:8081->80/tcp, :::8081->80/tcp   nginx
54e33277ea3f   httpd:2.4   "httpd-foreground"       3 minutes ago   Up 3 minutes   0.0.0.0:8080->80/tcp, :::8080->80/tcp   httpd

[cloud_user@ip-10-0-1-193 web]$ docker stop nginx
nginx

[cloud_user@ip-10-0-1-193 web]$ docker rm nginx
nginx

[cloud_user@ip-10-0-1-193 web]$ docker stop httpd
httpd

[cloud_user@ip-10-0-1-193 web]$ docker rm httpd
httpd

[cloud_user@ip-10-0-1-193 web]$ docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
[cloud_user@ip-10-0-1-193 web]$

```