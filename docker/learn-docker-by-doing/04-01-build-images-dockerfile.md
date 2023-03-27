# Building Container Images Using Dockerfiles

Creating a container image by hand is possible, but it requires manual processes. There has to be a more automatic way to build images. Manual processes do not scale and are not easily version controlled. Docker provides a solution to this problem - the Dockerfile. In this lab, you will create a Dockerfile to build an image to host a static website.

[Source](https://learn.acloud.guru/handson/ff4295a9-c1a8-45c7-8c8b-47681b909434/course/108f9d35-6966-4fb6-a6cd-7af135329bba "Permalink to Building Container Images Using Dockerfiles - A Cloud Guru")

## Scenario

You have been tasked with migrating your company's website to a container, to help meet increased demand, and improve scalability.

You've performed multiple experiments with the website, and the container, but it's time to standardize the process and make it repeatable. This means you need a way to code and automate image creation. Docker provides an easy solution: the Dockerfile. Create a Dockerfile that will generate a container image for serving your website.

The Widget Factory website is available from [GitHub](https://github.com/linuxacademy/content-widget-factory-inc) and has already been cloned into the `cloud_user`'s home directory. You will use this website code in the image.

## Introduction

Creating a container image by hand is possible, but it requires manual processes. There has to be a more automatic way to build images. Manual processes do not scale and are not easily version controlled. Docker provides a solution to this problem - the Dockerfile. In this lab, you will create a Dockerfile to build an image, and host a static website.

## Solution

Log in to the server using the credentials provided:

`ssh cloud_user@<PUBLIC_IP_ADDRESS>`

### Build a First Version

1. Change to the `widget-factory-inc` directory:
    
    `cd widget-factory-inc`
    
2. Create a Dockerfile that uses `httpd:2.4` as the base image:
    
    `vim Dockerfile`
    
3. In the new file, insert the following:
    
    `FROM httpd:2.4
    RUN apt update -y && apt upgrade -y && apt autoremove -y && apt clean && rm -rf /var/lib/apt/lists*`
    
4. Save the file:
    
    `ESC
    :wq`
    
5. Verify that the file was saved successfully:
    
    `cat Dockerfile`
    
6. Build the `0.1` version of the `widgetfactory` image using the Dockerfile:
    
    `docker build -t widgetfactory:0.1 .`
    
7. Set variables to examine the image's size and layers:
    
    `export showLayers='{{ range .RootFS.Layers }}{{ println . }}{{end}}'`
    
    `export showSize='{{ .Size }}'`
    
8. Compare the `httpd` and `widgetfactory` images:
    
    `docker images`
    
9. Show the `widgetfactory` image's size:
    
    `docker inspect -f "$showSize" widgetfactory:0.1`
    
10. Show the layers:
    
    `docker inspect -f "$showLayers" widgetfactory:0.1`
    
11. Show the layers of the `httpd:2.4` image:
    
    `docker inspect -f "$showLayers" httpd:2.4`
    
12. Compare the layers. Are they the same?

### Load the Website into the Container

1. Open the `Dockerfile`:
    
    `vim Dockerfile`
    
2. Remove the Apache welcome page from the image by adding the following:
    
    `RUN rm -f /usr/local/apache2/htdocs/index.html`
    
3. Save the file:
    
    `ESC
    :wq`
    
4. Build version `0.2` of the widgetfactory image:
    
    `docker build -t widgetfactory:0.2 .`
    
5. Inspect both versions of the `widgetfactory` image to see the differences in size and layers:
    
    `docker images`
    
6. Show the `widgetfactory:0.1` image's size:
    
    `docker inspect -f "$showSize" widgetfactory:0.1`
    
7. Compare it to the image size for `widgetfactory:0.2`:
    
    `docker inspect -f "$showSize" widgetfactory:0.2`
    
8. Using an interactive terminal, check the `htdocs` folder for `widgetfactory:0.2`. Are the website files in the folder?:
    
    `docker run --rm -it widgetfactory:0.2 bash`
    
    `ls htdocs`
    
9. Exit the container:
    
    `exit`
    
10. Show the layers for the `widgetfactory:0.1` image:
    
    `docker inspect -f "$showLayers" widgetfactory:0.1`
    
11. Show the layers for the `widgetfactory:0.2` image and compare the two:
    
    `docker inspect -f "$showLayers" widgetfactory:0.2`
    
12. Open the Dockerfile:
    
    `vim Dockerfile`
    
13. Add the website data to the container by adding the following to the end of the file:
    
    `WORKDIR /usr/local/apache2/htdocs
    COPY ./web .`
    
14. Save the file:
    
    `ESC
    :wq`
    
15. Build version `0.3` of the widgetfactory image:
    
    `docker build -t widgetfactory:0.3 .`
    
16. Inspect versions `0.2` and `0.3` to see the differences in size and layers:
    
    `docker images`
    
17. Show the `widgetfactory:0.2` image's size:
    
    `docker inspect -f "$showSize" widgetfactory:0.2`
    
18. Compare it to the image size for `widgetfactory:0.3`:
    
    `docker inspect -f "$showSize" widgetfactory:0.3`
    
19. Show the layers for the `widgetfactory:0.2` image:
    
    `docker inspect -f "$showLayers" widgetfactory:0.2`
    
20. Show the layers for the `widgetfactory:0.3` image and compare the two:
    
    `docker inspect -f "$showLayers" widgetfactory:0.3`
    
21. Using an interactive terminal, check the `htdocs` folder for `widgetfactory:0.3`:
    
    `docker run --rm -it widgetfactory:0.3 bash`
    
22. Are the website files in the folder?:
    
    `ls -l`
    

### Run a Container from the Image

1. Run a container from the `widgetfactory:0.3` image. What commmand does it use to run the server? Remember to publish the web server port:
    
    `docker run --name web1 -p 80:80 widgetfactory:0.3`
    
2. Exit the container:
    
    `CTRL+C`
    
3. Check the status of the container:
    
    `docker ps -a`
    
4. Start the container:
    
    `docker start web1`
    
5. Using `docker exec` connect to the `web1` container:
    
    `docker exec -it web1 bash`
    
6. View the website files in the container:
    
    `ls -l`
    
7. Exit the container:
    
    `exit`
    
8. Retrieve the main website page from the container:
    
    `wget localhost`
    
9. Compare it to the copy on the server:
    
    `diff index.html web/index.html`
    

### Read Me

> Note: Many of the things done in this lab are intentionally inefficient, These tasks are done to demonstrate certain aspects of Docker, and highlight common pitfalls. These do not represent best practices.
>

# LOG

```sh
/ $ ssh cloud_guru@3.235.136.226

Host '3.235.136.226' is not in the trusted hosts file.
(ssh-ed25519 fingerprint sha1!! cc:60:02:bd:cd:43:d7:ee:b9:93:23:1d:45:b9:87:b1:3b:59:41:0e)
Do you want to continue connecting? (y/n) y
cloud_guru@3.235.136.226's password:
cloud_guru@3.235.136.226's password:

ssh: Connection to cloud_guru@3.235.136.226:22 exited: Interrupted.
/ $ ssh cloud_user@3.235.136.226
cloud_user@3.235.136.226's password:
[cloud_user@ip-10-0-1-148 ~]$ cd widget-factory-inc
[cloud_user@ip-10-0-1-148 widget-factory-inc]$ pwd
/home/cloud_user/widget-factory-inc
[cloud_user@ip-10-0-1-148 widget-factory-inc]$ vim Dockerfile
[cloud_user@ip-10-0-1-148 widget-factory-inc]$ cat Dockerfile
FROM httpd:2.4

RUN apt update -y && apt upgrade -y && apt autoremove -y && apt clean && rm -rf /var/lib/apt/lists*


[cloud_user@ip-10-0-1-148 widget-factory-inc]$ docker build -t widgetfactory:0.1 .
[+] Building 10.4s (6/6) FINISHED
 => [internal] load build definition from Dockerfile                                                                                                    0.0s
 => => transferring dockerfile: 214B                                                                                                                    0.0s
 => [internal] load .dockerignore                                                                                                                       0.0s
 => => transferring context: 2B                                                                                                                         0.0s
 => [internal] load metadata for docker.io/library/httpd:2.4                                                                                            0.0s
 => [1/2] FROM docker.io/library/httpd:2.4                                                                                                              0.1s
 => [2/2] RUN apt update -y && apt upgrade -y && apt autoremove -y && apt clean && rm -rf /var/lib/apt/lists*                                          10.1s
 => exporting to image                                                                                                                                  0.2s
 => => exporting layers                                                                                                                                 0.2s
 => => writing image sha256:bf0e426c9414283e673fec1e978ef3ad0e7b52b16c1a06523f4d75ba52055477                                                            0.0s
 => => naming to docker.io/library/widgetfactory:0.1                                                                                                    0.0s
[cloud_user@ip-10-0-1-148 widget-factory-inc]$

```

See [dockerfile](./04-01/Dockerfile)

```sh
[cloud_user@ip-10-0-1-148 widget-factory-inc]$ export showLayers='{{ range .RootFS.Layers }}{{ println . }}{{end}}'
[cloud_user@ip-10-0-1-148 widget-factory-inc]$ export showSize='{{ .Size }}'

[cloud_user@ip-10-0-1-148 widget-factory-inc]$ docker images
REPOSITORY      TAG       IMAGE ID       CREATED              SIZE
widgetfactory   0.1       bf0e426c9414   About a minute ago   149MB
httpd           2.4       192d41583429   4 days ago           145MB

[cloud_user@ip-10-0-1-148 widget-factory-inc]$ docker inspect -f "$showSize" widgetfactory:0.1
149332852

[cloud_user@ip-10-0-1-148 widget-factory-inc]$ docker inspect -f "$showLayers" widgetfactory:0.1
sha256:3af14c9a24c941c626553628cf1942dcd94d40729777f2fcfbcd3b8a3dfccdd6
sha256:d0dbdb0bf1f709f4b1a3e0d1708bcbaea360c9a9f5281439cfcfcc55e3ed8cd3
sha256:8065d3aedc6d597a95576135e68ebfe03fedc190645e3a53b716ca30ad18309b
sha256:626b836c6b3cc4b68e492082a6a2c635551ac5eca97b124195e62239842c5a39
sha256:10f56d9b82f42fd376ea5802b078d13b60a8b840293d26d7eb7828af84911cc7
sha256:5384ecc1d37e2ac4849f3d98f6a2568939f976494cbf4666b6820c51a4dd10e0

[cloud_user@ip-10-0-1-148 widget-factory-inc]$ docker inspect -f "$showLayers" httpd:2.4
sha256:3af14c9a24c941c626553628cf1942dcd94d40729777f2fcfbcd3b8a3dfccdd6
sha256:d0dbdb0bf1f709f4b1a3e0d1708bcbaea360c9a9f5281439cfcfcc55e3ed8cd3
sha256:8065d3aedc6d597a95576135e68ebfe03fedc190645e3a53b716ca30ad18309b
sha256:626b836c6b3cc4b68e492082a6a2c635551ac5eca97b124195e62239842c5a39
sha256:10f56d9b82f42fd376ea5802b078d13b60a8b840293d26d7eb7828af84911cc7

```

## Version 2

```sh
[cloud_user@ip-10-0-1-148 widget-factory-inc]$ vim Dockerfile
[cloud_user@ip-10-0-1-148 widget-factory-inc]$ cat Dockerfile
FROM httpd:2.4

RUN apt update -y && apt upgrade -y && apt autoremove -y && apt clean && rm -rf /var/lib/apt/lists*
RUN rm -f /usr/local/apache2/htdocs/index.html


[cloud_user@ip-10-0-1-148 widget-factory-inc]$ docker build -t widgetfactory:0.2 .
[+] Building 0.4s (7/7) FINISHED
 => [internal] load build definition from Dockerfile                                                                                                    0.0s
 => => transferring dockerfile: 263B                                                                                                                    0.0s
 => [internal] load .dockerignore                                                                                                                       0.0s
 => => transferring context: 2B                                                                                                                         0.0s
 => [internal] load metadata for docker.io/library/httpd:2.4                                                                                            0.0s
 => [1/3] FROM docker.io/library/httpd:2.4                                                                                                              0.0s
 => CACHED [2/3] RUN apt update -y && apt upgrade -y && apt autoremove -y && apt clean && rm -rf /var/lib/apt/lists*                                    0.0s
 => [3/3] RUN rm -f /usr/local/apache2/htdocs/index.html                                                                                                0.3s
 => exporting to image                                                                                                                                  0.0s
 => => exporting layers                                                                                                                                 0.0s
 => => writing image sha256:b5a8985a4ba6aa79091ed281947ff6a725bf5ee5c3c1dcf3488430bab2192844                                                            0.0s
 => => naming to docker.io/library/widgetfactory:0.2                                                                                                    0.0s
[cloud_user@ip-10-0-1-148 widget-factory-inc]$ docker images
REPOSITORY      TAG       IMAGE ID       CREATED          SIZE
widgetfactory   0.2       b5a8985a4ba6   11 seconds ago   149MB
widgetfactory   0.1       bf0e426c9414   5 minutes ago    149MB
httpd           2.4       192d41583429   4 days ago       145MB
[cloud_user@ip-10-0-1-148 widget-factory-inc]$ docker inspect -f "$showSize" widgetfactory:0.1
149332852
[cloud_user@ip-10-0-1-148 widget-factory-inc]$ docker inspect -f "$showSize" widgetfactory:0.2
149332852
[cloud_user@ip-10-0-1-148 widget-factory-inc]$ docker run --rm -it widgetfactory:0.2 bash
root@755d24eda60d:/usr/local/apache2# ls htdocs
root@755d24eda60d:/usr/local/apache2# exit
exit
[cloud_user@ip-10-0-1-148 widget-factory-inc]$ docker inspect -f "$showLayers" widgetfactory:0.1
sha256:3af14c9a24c941c626553628cf1942dcd94d40729777f2fcfbcd3b8a3dfccdd6
sha256:d0dbdb0bf1f709f4b1a3e0d1708bcbaea360c9a9f5281439cfcfcc55e3ed8cd3
sha256:8065d3aedc6d597a95576135e68ebfe03fedc190645e3a53b716ca30ad18309b
sha256:626b836c6b3cc4b68e492082a6a2c635551ac5eca97b124195e62239842c5a39
sha256:10f56d9b82f42fd376ea5802b078d13b60a8b840293d26d7eb7828af84911cc7
sha256:5384ecc1d37e2ac4849f3d98f6a2568939f976494cbf4666b6820c51a4dd10e0

[cloud_user@ip-10-0-1-148 widget-factory-inc]$ docker inspect -f "$showLayers" widgetfactory:0.2
sha256:3af14c9a24c941c626553628cf1942dcd94d40729777f2fcfbcd3b8a3dfccdd6
sha256:d0dbdb0bf1f709f4b1a3e0d1708bcbaea360c9a9f5281439cfcfcc55e3ed8cd3
sha256:8065d3aedc6d597a95576135e68ebfe03fedc190645e3a53b716ca30ad18309b
sha256:626b836c6b3cc4b68e492082a6a2c635551ac5eca97b124195e62239842c5a39
sha256:10f56d9b82f42fd376ea5802b078d13b60a8b840293d26d7eb7828af84911cc7
sha256:5384ecc1d37e2ac4849f3d98f6a2568939f976494cbf4666b6820c51a4dd10e0
sha256:505fe7cb52ec2f490346007acf8bff1b69631050053371e53f8eadd09ecffc0a

```