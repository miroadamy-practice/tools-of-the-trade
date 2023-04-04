# Updating Containers with Watchtower

For the last six months, the Acme Anvil Corporation has been migrating some of their bare metal infrastructure to Docker containers. After the initial implementation, the team has decided that they need a way to manage container updates.

After doing some research, the team has discovered Watchtower. Watchtower is a container that updates all running containers when changes are made to the image that it is running.

You have been tasked with creating a proof of concept for the team. You will need to create a Dockerfile that will be used to create a Docker image. The image will be pushed to Docker Hub. Next, you will create a container using this image. Once the image is created, you will deploy the Watchtower container. After Watchtower is deployed, you will update the Dockerfile, rebuild the image, and push the changes to Docker Hub. Watchtower checks for changes every 30 seconds. Once it detects the changes, Watchtower will update the running container.

For more information on Watchtower you can go here.

LEARNING OBJECTIVES

* Create the Dockerfile
* Build the Docker Image
* Push the Image to Docker Hub
* Create a Demo Container
* Create the Watchtower Container
* Update the Docker Image

## Introduction

Welcome to the lab! We'll be using Watchtower to monitor containers for updates, and then update them with the newer versions that are sitting up in Docker Hub.

### Prerequisites

In order to complete this learning activity, you will need a [Docker Hub](https://hub.docker.com/) account.

## Starting Off

Let's log into the live environment and become root (`sudo su -`)

## Create the Docker file

Let's create the Docker file in a text editor (we're using `vi`):

`vi Dockerfile`

We need to set these parameters and conditions:

- The base image should be `node`.
- Using the `RUN` instruction, make a directory called `/var/node`.
- Use the `ADD` instruction to add the contents of the code directory into `/var/node`.
- Make `/var/node` the working directory.
- Execute an `npm install`.
- Set `./bin/www` as the command.
- From the command line, log in to Docker Hub.
- Build your image using `<USERNAME>/express`.
- Push the image to Docker Hub.

When we're finished, the file should look like this:

`FROM node

RUN mkdir -p /var/node
ADD content-express-demo-app/ /var/node/
WORKDIR /var/node
RUN npm install
CMD ./bin/www`

## Log in to Docker Hub

We've got to build our Docker image, but before we do that, let's get logged into our Docker Hub account. Do it from the command line, like this:

`docker login`

## Build the Docker Image

Now we can build the image, where `<USERNAME>` in the command is your Docker Hub username:

`docker build -t <USERNAME>/express -f Dockerfile .`

## Push the Image to Docker Hub

Push the image up to Docker Hub with this:

`docker push <USERNAME>/express`

If we log into the Docker Hub with a web browser, we should see the image in our repository.

## Create the Demo Container

In order for Watchtower to watch anything, we've got to give it something to keep an eye on. We're going to create a Docker container and set some parameters, all in one command:

`docker run -d --name demo-app -p 80:3000 --restart always <USERNAME>/express`

To make sure it's running, let's execute a quick `docker ps`. We should see it in the list.

## Create the Watchtower Container

Now we'll spin up Watchtower to watch our `demo-app` container. We can create the Watchtower container with this:

`docker run -d --name watchtower --restart always -v /var/run/docker.sock:/var/run/docker.sock v2tec/watchtower -i 30`

Again, run a `docker ps` real quick to make sure this container spun up properly.

## Testing

We want to see if Watchtower is actually keeping watch, so we've got to take a couple more steps.

### Update the Docker Image

We're going to edit `Dockererfile`, rebuild the image, then push the new one up to Docker Hub.

Edit `Dockerimage`, and let's make a simple change, like a `mkdir` command. Add it below the existing one, so that the file looks like this now:

```FROM node

RUN mkdir -p /var/node
RUN mkdir -p /var/test
ADD content-express-demo-app/ /var/node/
WORKDIR /var/node
RUN npm install
CMD ./bin/www
```

Rebuild the image with this:

`docker build -t <USERNAME>/express -f Dockerfile .`

Now push it up to Docker Hub:

`docker push <USERNAME>/express`

## Watch the Magic of Watchtower

Since we set the interval at 30 seconds (the `-i` in the last `docker run` command we ran), then our Watchtower container should be checking for updates every 30 seconds. And if we run `docker ps` in a little bit, we're going to see that the `demo-app` container gets updated.

# LOG

```sh

[cloud_user@ip-10-0-1-67 ~]$ sudo su -
[sudo] password for cloud_user:
Last failed login: Tue Apr  4 17:23:29 EDT 2023 from 180.76.172.241 on ssh:notty
There were 28 failed login attempts since the last successful login.

[root@ip-10-0-1-67 ~]# vi Dockerfile

[root@ip-10-0-1-67 ~]# cat Dockerfile
FROM node

RUN mkdir -p /var/node
ADD content-express-demo-app/ /var/node/
WORKDIR /var/node
RUN npm install
CMD ./bin/www



```

```sh
[root@ip-10-0-1-67 ~]# ll content-express-demo-app/
total 8
-rw-r--r--. 1 root root 1257 Apr  4 16:05 app.js
drwxr-xr-x. 2 root root   17 Apr  4 16:05 bin
-rw-r--r--. 1 root root  326 Apr  4 16:05 package.json
drwxr-xr-x. 3 root root   25 Apr  4 16:05 public
drwxr-xr-x. 2 root root   38 Apr  4 16:05 routes
drwxr-xr-x. 2 root root   61 Apr  4 16:05 views
[root@ip-10-0-1-67 ~]# cat content-express-demo-app/app.js
var express = require('express');
var path = require('path');
var favicon = require('serve-favicon');
var logger = require('morgan');
var cookieParser = require('cookie-parser');
var bodyParser = require('body-parser');

var index = require('./routes/index');
var users = require('./routes/users');

var app = express();

// view engine setup
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'jade');

// uncomment after placing your favicon in /public
//app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
app.use(logger('dev'));
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: false }));
app.use(cookieParser());
app.use(express.static(path.join(__dirname, 'public')));

app.use('/', index);
app.use('/users', users);

// catch 404 and forward to error handler
app.use(function(req, res, next) {
  var err = new Error('Not Found');
  err.status = 404;
  next(err);
});

// error handler
app.use(function(err, req, res, next) {
  // set locals, only providing error in development
  res.locals.message = err.message;
  res.locals.error = req.app.get('env') === 'development' ? err : {};

  // render the error page
  res.status(err.status || 500);
  res.render('error');
});

module.exports = app;

```

I will use miroadamy as my pers user

```sh
[root@ip-10-0-1-67 ~]# docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: miroadamy
Password:
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
[root@ip-10-0-1-67 ~]# docker build -t miroadamy/express -f Dockerfile .
Sending build context to Docker daemon  114.7kB
Step 1/6 : FROM node
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
Step 2/6 : RUN mkdir -p /var/node
 ---> Running in 3e99f64cf17e
Removing intermediate container 3e99f64cf17e
 ---> 2c928f54e2c1
Step 3/6 : ADD content-express-demo-app/ /var/node/
 ---> 96e04e16d375
Step 4/6 : WORKDIR /var/node
 ---> Running in 7b4be9ea2c66
Removing intermediate container 7b4be9ea2c66
 ---> 20903142598f
Step 5/6 : RUN npm install
 ---> Running in 3739af153927
npm WARN deprecated transformers@2.1.0: Deprecated, use jstransformer
npm WARN deprecated constantinople@3.0.2: Please update to at least constantinople 3.1.1
npm WARN deprecated jade@1.11.0: Jade has been renamed to pug, please install the latest version of pug instead of jade

added 104 packages, and audited 105 packages in 6s

1 package is looking for funding
  run `npm fund` for details

11 vulnerabilities (1 low, 3 moderate, 3 high, 4 critical)

To address all issues (including breaking changes), run:
  npm audit fix --force

Run `npm audit` for details.
npm notice
npm notice New minor version of npm available! 9.5.1 -> 9.6.3
npm notice Changelog: <https://github.com/npm/cli/releases/tag/v9.6.3>
npm notice Run `npm install -g npm@9.6.3` to update!
npm notice
Removing intermediate container 3739af153927
 ---> 5c6fb05fa545
Step 6/6 : CMD ./bin/www
 ---> Running in 1237a74f81d0
Removing intermediate container 1237a74f81d0
 ---> 96fe6998ff6e
Successfully built 96fe6998ff6e
Successfully tagged miroadamy/express:latest
[root@ip-10-0-1-67 ~]#

[root@ip-10-0-1-67 ~]# docker push miroadamy/express
Using default tag: latest
The push refers to repository [docker.io/miroadamy/express]
2910c900d701: Pushed
94776e293e5f: Pushed
48fc0ebab3a3: Pushed
a229f6e7a615: Mounted from library/node
d25cfc53cd3f: Mounted from library/node
bf55b14248a7: Mounted from library/node
7efd70d0bc36: Mounted from library/node
d4514f8b2aac: Mounted from library/node
5ab567b9150b: Mounted from library/node
a90e3914fb92: Mounted from library/node
053a1f71007e: Mounted from library/node
ec09eb83ea03: Mounted from library/node
latest: digest: sha256:e19576fdb741d01d3ebe550841205b342b6c464da5f6f83b4c806bd363be7cc7 size: 2842
```

Run the container

```sh
[root@ip-10-0-1-67 ~]# docker run -d --name demo-app -p 80:3000 --restart always miroadamy/express
70731c32901b2e5dc6f80ffc1528d4076b0377bc632608992d489062f15cfa2c

[root@ip-10-0-1-67 ~]# docker ps
CONTAINER ID   IMAGE               COMMAND                  CREATED         STATUS         PORTS                                   NAMES
70731c32901b   miroadamy/express   "docker-entrypoint.s…"   5 seconds ago   Up 2 seconds   0.0.0.0:80->3000/tcp, :::80->3000/tcp   demo-app

# Run watchtower

[root@ip-10-0-1-67 ~]# docker run -d --name watchtower --restart always -v /var/run/docker.sock:/var/run/docker.sock v2tec/watchtower -i 30
Unable to find image 'v2tec/watchtower:latest' locally
latest: Pulling from v2tec/watchtower
a5415f98d52c: Pull complete
c3f7208ad77c: Pull complete
169c1e589d74: Pull complete
Digest: sha256:4cb6299fe87dcbfe0f13dcc5a11bf44bd9628a4dae0035fecb8cc2b88ff0fc79
Status: Downloaded newer image for v2tec/watchtower:latest
27b473622925818ec60263491d9aaa8f709c1e762ec647c18d4c2f82aff9df57

[root@ip-10-0-1-67 ~]#
[root@ip-10-0-1-67 ~]# docker ps
CONTAINER ID   IMAGE               COMMAND                  CREATED              STATUS              PORTS                                   NAMES
27b473622925   v2tec/watchtower    "/watchtower -i 30"      35 seconds ago       Up 33 seconds                                               watchtower
70731c32901b   miroadamy/express   "docker-entrypoint.s…"   About a minute ago   Up About a minute   0.0.0.0:80->3000/tcp, :::80->3000/tcp   demo-app
[root@ip-10-0-1-67 ~]#


```

We want to see if Watchtower is actually keeping watch, so we've got to take a couple more steps.

Change the dockerfile

```sh
[root@ip-10-0-1-67 ~]# vi Dockerfile

[root@ip-10-0-1-67 ~]# cat Dockerfile
FROM node

RUN mkdir -p /var/node
RUN mkdir -p /var/test
ADD content-express-demo-app/ /var/node/
WORKDIR /var/node
RUN npm install
CMD ./bin/www


[root@ip-10-0-1-67 ~]#
[root@ip-10-0-1-67 ~]# docker build -t miroadamy/express -f Dockerfile .
Sending build context to Docker daemon  114.7kB
Step 1/7 : FROM node
 ---> 0e0ab07dbedd
Step 2/7 : RUN mkdir -p /var/node
 ---> Using cache
 ---> 2c928f54e2c1
Step 3/7 : RUN mkdir -p /var/test
 ---> Running in f8b9ceed3d8f
Removing intermediate container f8b9ceed3d8f
 ---> 03246d8d130b
Step 4/7 : ADD content-express-demo-app/ /var/node/
 ---> deca33b6ed49
Step 5/7 : WORKDIR /var/node
 ---> Running in 3f7db54400ed
Removing intermediate container 3f7db54400ed
 ---> f212311a4749
Step 6/7 : RUN npm install
 ---> Running in 8a242f65bcac
npm WARN deprecated transformers@2.1.0: Deprecated, use jstransformer
npm WARN deprecated constantinople@3.0.2: Please update to at least constantinople 3.1.1
npm WARN deprecated jade@1.11.0: Jade has been renamed to pug, please install the latest version of pug instead of jade

added 104 packages, and audited 105 packages in 5s

1 package is looking for funding
  run `npm fund` for details

11 vulnerabilities (1 low, 3 moderate, 3 high, 4 critical)

To address all issues (including breaking changes), run:
  npm audit fix --force

Run `npm audit` for details.
npm notice
npm notice New minor version of npm available! 9.5.1 -> 9.6.3
npm notice Changelog: <https://github.com/npm/cli/releases/tag/v9.6.3>
npm notice Run `npm install -g npm@9.6.3` to update!
npm notice
Removing intermediate container 8a242f65bcac
 ---> 9c18419748fa
Step 7/7 : CMD ./bin/www
 ---> Running in a95b94324704
Removing intermediate container a95b94324704
 ---> 54e1fb22d5ca
Successfully built 54e1fb22d5ca
Successfully tagged miroadamy/express:latest
[root@ip-10-0-1-67 ~]#
[root@ip-10-0-1-67 ~]#
[root@ip-10-0-1-67 ~]# docker push miroadamy/express
Using default tag: latest
The push refers to repository [docker.io/miroadamy/express]
b5654d500ab7: Pushed
27bb7de020d6: Pushed
659dc4aa7c7e: Pushed
48fc0ebab3a3: Layer already exists
a229f6e7a615: Layer already exists
d25cfc53cd3f: Layer already exists
bf55b14248a7: Layer already exists
7efd70d0bc36: Layer already exists
d4514f8b2aac: Layer already exists
5ab567b9150b: Layer already exists
a90e3914fb92: Layer already exists
053a1f71007e: Layer already exists
ec09eb83ea03: Layer already exists
latest: digest: sha256:f292543aba1d2313f3518811356ba588c95f981a95a93677995c8d93416ed243 size: 3049
[root@ip-10-0-1-67 ~]#
```

running app gets updated

```sh
[root@ip-10-0-1-67 ~]# docker ps
CONTAINER ID   IMAGE                      COMMAND                  CREATED         STATUS         PORTS                                   NAMES
c15cce03b032   miroadamy/express:latest   "docker-entrypoint.s…"   4 minutes ago   Up 3 minutes   0.0.0.0:80->3000/tcp, :::80->3000/tcp   demo-app
27b473622925   v2tec/watchtower           "/watchtower -i 30"      8 minutes ago   Up 8 minutes                                           watchtower
```