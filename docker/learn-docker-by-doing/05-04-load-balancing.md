# Load Balancing Containers

## Introduction

For the last six months, the Acme Anvil Corporation has been migrating some of their bare metal infrastructure to Docker containers. The initial implementation was very basic and lacked any kind of load balancing. Your manager has tasked you with creating two proofs of concept. For the first proof of concept, you are to use Docker Compose to create an Nginx load balancer and three instances using your `weather-app` image. Nginx will use port 80 and send traffic to port 3000 on the `weather-app` containers. For the second proof of concept, you are to create a Docker Swarm service called `nginx-app` that has two replicas using the Nginx image. The service should be published to port 8080 on the host and target port 80 on the containers.

## Solution

Begin by logging in to both lab servers using the credentials provided on the hands-on lab page:

`ssh cloud_user@PUBLIC_IP_ADDRESS`

Become the `root` user:

`sudo su -`

### Create a Docker Compose file

On **Swarm Server 1**

1. Change to the `lb-challenge` directory:
    
    `cd lb-challenge`
    
2. Create our Docker compose file:
    
    `vi docker-compose.yml`
    
3. The contents of your `docker-compose.yml` file should look like the following:
    
    `version: '3.2'
    services:
      weather-app1:
          build: ./weather-app
          tty: true
          networks:
           - frontend
      weather-app2:
          build: ./weather-app
          tty: true
          networks:
           - frontend
      weather-app3:
          build: ./weather-app
          tty: true
          networks:
           - frontend
    
      loadbalancer:
          build: ./load-balancer
          image: nginx
          tty: true
          ports:
           - '80:80'
          networks:
           - frontend
    
    networks:
      frontend:`
    

### Update `nginx.conf`

1. Change to the `load-balancer` directory:
    
    `cd load-balancer`
    
2. Edit the `nginx.conf` file:
    
    `vi nginx.conf`
    
3. The contents of your `nginx.conf` file should look like the following:
    
    `events { worker_connections 1024; }
    
    http {
      upstream localhost {
        server weather-app1:3000;
        server weather-app2:3000;
        server weather-app3:3000;
      }
      server {
        listen 80;
        server_name localhost;
        location / {
          proxy_pass http://localhost;
          proxy_set_header Host $host;
        }
      }
    }`
    

### Execute `docker-compose up`

1. Change directories:
    
    `cd ../`
    
2. Execute a `docker-compose up`:
    
    `docker-compose up --build -d`
    
3. Check our work:
    
    `docker ps`
    
4. Copy the public IP address of **Swarm Server 1** and paste it into a new tab in your browser.

### Create a Docker service using Docker Swarm

1. Change to the `root` directory:
    
    `cd ~/`
    
2. View the contents of `swarm-token.txt`:
    
    `cat swarm-token.txt`
    
3. Copy the `docker swarm join` command from the previous step.

On **Swarm Server 2**

1. Execute the command that was copied from the previous step.

On **Swarm Server 1**

1. Create a Docker service by executing the following command:
    
    `docker service create --name nginx-app --publish published=8080,target=80 --replicas=2 nginx`
    
2. Verify this completed successfully by running the following command on both servers:
    
    `docker ps`
    
3. Verify that the default nginx page loads in the browser:
    
    `PUBLIC_IP_ADDRESS:8080`
    

## Conclusion

Congratulations, you've completed this hands-on lab!

# LOG

Source:

```sh
[root@ip-10-0-1-62 lb-challenge]# git remote -v
origin  https://github.com/linuxacademy/content-intermediate-docker-quest-lb.git (fetch)
origin  https://github.com/linuxacademy/content-intermediate-docker-quest-lb.git (push)
```

Important is version 3.2

```sh
Host '34.205.135.22' is not in the trusted hosts file.
(ssh-ed25519 fingerprint sha1!! 8e:d5:79:bd:f7:02:b0:a8:47:22:87:75:19:4a:0f:ca:29:88:de:bf)
Do you want to continue connecting? (y/n) y
cloud_user@34.205.135.22's password:

[cloud_user@ip-10-0-1-62 ~]$ sudo su -

[sudo] password for cloud_user:
Last failed login: Fri Apr  7 11:57:33 EDT 2023 from 111.93.227.210 on ssh:notty
There were 3 failed login attempts since the last successful login.

[root@ip-10-0-1-62 ~]# cd lb-challenge/
[root@ip-10-0-1-62 lb-challenge]#

[root@ip-10-0-1-62 lb-challenge]# vi docker-compose.yml
[root@ip-10-0-1-62 lb-challenge]# cat docker-compose.yml
version: '3.2'
services:
  weather-app1:
      build: ./weather-app
      tty: true
      networks:
       - frontend
  weather-app2:
      build: ./weather-app
      tty: true
      networks:
       - frontend
  weather-app3:
      build: ./weather-app
      tty: true
      networks:
       - frontend

  loadbalancer:
      build: ./load-balancer
      image: nginx
      tty: true
      ports:
       - '80:80'
      networks:
       - frontend

networks:
  frontend:

# ----

[root@ip-10-0-1-62 lb-challenge]# cat load-balancer/Dockerfile
FROM nginx

COPY nginx.conf /etc/nginx/nginx.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]

# ----

root@ip-10-0-1-62 lb-challenge]# cat weather-app/Dockerfile
FROM node

LABEL maintainer="pinehead@linuxacademy.com"
LABEL org.label-schema.applicaiton=weather-app
LABEL org.label-schema.version=v1.1

RUN mkdir -p /var/node
ADD src/ /var/node/
WORKDIR /var/node
RUN npm install
EXPOSE 3000
CMD ./bin/www

# ---- 

[root@ip-10-0-1-62 lb-challenge]#
[root@ip-10-0-1-62 lb-challenge]# cd load-balancer/
[root@ip-10-0-1-62 load-balancer]# cat nginx.conf
events { worker_connections 1024; }

http {
 upstream localhost {
    # Weather app config goes here.
 }
 server {
    # Server Config goes here.
  }
}
[root@ip-10-0-1-62 load-balancer]# vi nginx.conf
[root@ip-10-0-1-62 load-balancer]#
[root@ip-10-0-1-62 load-balancer]# cat nginx.conf
events { worker_connections 1024; }

http {
  upstream localhost {
    server weather-app1:3000;
    server weather-app2:3000;
    server weather-app3:3000;
  }
  server {
    listen 80;
    server_name localhost;
    location / {
      proxy_pass http://localhost;
      proxy_set_header Host $host;
    }
  }
}
[root@ip-10-0-1-62 load-balancer]#


```

Run

```sh
[root@ip-10-0-1-62 load-balancer]# cd ..
[root@ip-10-0-1-62 lb-challenge]# ll
total 4
-rw-r--r--. 1 root root 472 Apr  7 12:07 docker-compose.yml
drwxr-xr-x. 2 root root  42 Apr  7 12:09 load-balancer
drwxr-xr-x. 3 root root  35 Apr  7 11:24 weather-app
[root@ip-10-0-1-62 lb-challenge]# docker-compose up --build -d
WARNING: The Docker Engine you're using is running in swarm mode.

Compose does not use swarm mode to deploy services to multiple nodes in a swarm. All containers will be scheduled on the current node.

To deploy your application across the swarm, use `docker stack deploy`.

Creating network "lb-challenge_frontend" with the default driver
Building weather-app1
Step 1/10 : FROM node
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
Step 2/10 : LABEL maintainer="pinehead@linuxacademy.com"
 ---> Running in 4bd411ffea43
Removing intermediate container 4bd411ffea43
 ---> f871c53ae1ff
Step 3/10 : LABEL org.label-schema.applicaiton=weather-app
 ---> Running in 00160c8c85d6
Removing intermediate container 00160c8c85d6
 ---> 1e081e8e428f
Step 4/10 : LABEL org.label-schema.version=v1.1
 ---> Running in e07c3d49b2c4
Removing intermediate container e07c3d49b2c4
 ---> 3059aba750a0
Step 5/10 : RUN mkdir -p /var/node
 ---> Running in 45adaa714465
Removing intermediate container 45adaa714465
 ---> f998f9146568
Step 6/10 : ADD src/ /var/node/
 ---> 6298ea7f41a2
Step 7/10 : WORKDIR /var/node
 ---> Running in 9dbd04f7e9d7
Removing intermediate container 9dbd04f7e9d7
 ---> 91150e105f70
Step 8/10 : RUN npm install
 ---> Running in a09b9de45881
npm WARN old lockfile
npm WARN old lockfile The package-lock.json file was created with an old version of npm,
npm WARN old lockfile so supplemental metadata must be fetched from the registry.
npm WARN old lockfile
npm WARN old lockfile This is a one-time fix-up, please be patient...
npm WARN old lockfile
npm WARN deprecated har-validator@5.1.0: this library is no longer supported
npm WARN deprecated uuid@3.3.2: Please upgrade  to version 7 or higher.  Older versions may use Math.random() in certain circumstances, which is known to be problematic.  See https://v8.dev/blog/math-random for details.
npm WARN deprecated request@2.88.0: request has been deprecated, see https://github.com/request/request/issues/3142
npm WARN deprecated core-js@2.5.7: core-js@<3.23.3 is no longer maintained and not recommended for usage due to the number of issues. Because of the V8 engine whims, feature detection in old core-js versions could cause a slowdown up to 100x even if nothing is polyfilled. Some versions have web compatibility issues. Please, upgrade your dependencies to the actual version of core-js.

added 169 packages, and audited 170 packages in 7s

19 vulnerabilities (2 low, 8 moderate, 5 high, 4 critical)

To address issues that do not require attention, run:
  npm audit fix

To address all issues possible (including breaking changes), run:
  npm audit fix --force

Some issues need review, and may require choosing
a different dependency.

Run `npm audit` for details.
npm notice
npm notice New minor version of npm available! 9.5.1 -> 9.6.4
npm notice Changelog: <https://github.com/npm/cli/releases/tag/v9.6.4>
npm notice Run `npm install -g npm@9.6.4` to update!
npm notice
Removing intermediate container a09b9de45881
 ---> 80c307c8e69c
Step 9/10 : EXPOSE 3000
 ---> Running in 8fc8be637411
Removing intermediate container 8fc8be637411
 ---> de45af598861
Step 10/10 : CMD ./bin/www
 ---> Running in 40498a9279b5
Removing intermediate container 40498a9279b5
 ---> b8446662c1ce
Successfully built b8446662c1ce
Successfully tagged lb-challenge_weather-app1:latest
Building weather-app2
Step 1/10 : FROM node
 ---> 0e0ab07dbedd
Step 2/10 : LABEL maintainer="pinehead@linuxacademy.com"
 ---> Using cache
 ---> f871c53ae1ff
Step 3/10 : LABEL org.label-schema.applicaiton=weather-app
 ---> Using cache
 ---> 1e081e8e428f
Step 4/10 : LABEL org.label-schema.version=v1.1
 ---> Using cache
 ---> 3059aba750a0
Step 5/10 : RUN mkdir -p /var/node
 ---> Using cache
 ---> f998f9146568
Step 6/10 : ADD src/ /var/node/
 ---> Using cache
 ---> 6298ea7f41a2
Step 7/10 : WORKDIR /var/node
 ---> Using cache
 ---> 91150e105f70
Step 8/10 : RUN npm install
 ---> Using cache
 ---> 80c307c8e69c
Step 9/10 : EXPOSE 3000
 ---> Using cache
 ---> de45af598861
Step 10/10 : CMD ./bin/www
 ---> Using cache
 ---> b8446662c1ce
Successfully built b8446662c1ce
Successfully tagged lb-challenge_weather-app2:latest
Building weather-app3
Step 1/10 : FROM node
 ---> 0e0ab07dbedd
Step 2/10 : LABEL maintainer="pinehead@linuxacademy.com"
 ---> Using cache
 ---> f871c53ae1ff
Step 3/10 : LABEL org.label-schema.applicaiton=weather-app
 ---> Using cache
 ---> 1e081e8e428f
Step 4/10 : LABEL org.label-schema.version=v1.1
 ---> Using cache
 ---> 3059aba750a0
Step 5/10 : RUN mkdir -p /var/node
 ---> Using cache
 ---> f998f9146568
Step 6/10 : ADD src/ /var/node/
 ---> Using cache
 ---> 6298ea7f41a2
Step 7/10 : WORKDIR /var/node
 ---> Using cache
 ---> 91150e105f70
Step 8/10 : RUN npm install
 ---> Using cache
 ---> 80c307c8e69c
Step 9/10 : EXPOSE 3000
 ---> Using cache
 ---> de45af598861
Step 10/10 : CMD ./bin/www
 ---> Using cache
 ---> b8446662c1ce
Successfully built b8446662c1ce
Successfully tagged lb-challenge_weather-app3:latest
Building loadbalancer
Step 1/4 : FROM nginx
latest: Pulling from library/nginx
f1f26f570256: Pull complete
7f7f30930c6b: Pull complete
2836b727df80: Pull complete
e1eeb0f1c06b: Pull complete
86b2457cc2b0: Pull complete
9862f2ee2e8c: Pull complete
Digest: sha256:2ab30d6ac53580a6db8b657abf0f68d75360ff5cc1670a85acb5bd85ba1b19c0
Status: Downloaded newer image for nginx:latest
 ---> 080ed0ed8312
Step 2/4 : COPY nginx.conf /etc/nginx/nginx.conf
 ---> 769eb883366a
Step 3/4 : EXPOSE 80
 ---> Running in 4a850588ee11
Removing intermediate container 4a850588ee11
 ---> 960beb0facee
Step 4/4 : CMD ["nginx", "-g", "daemon off;"]
 ---> Running in 5dce9f65062e
Removing intermediate container 5dce9f65062e
 ---> bf767a916694
Successfully built bf767a916694
Successfully tagged nginx:latest
Creating lb-challenge_weather-app3_1 ... done
Creating lb-challenge_weather-app1_1 ... done
Creating lb-challenge_weather-app2_1 ... done
Creating lb-challenge_loadbalancer_1 ... done


[root@ip-10-0-1-62 lb-challenge]# docker ps
CONTAINER ID   IMAGE                       COMMAND                  CREATED              STATUS              PORTS                               NAMES
e94ad40cdd19   nginx                       "/docker-entrypoint.…"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, :::80->80/tcp   lb-challenge_loadbalancer_1
e6931755b1e6   lb-challenge_weather-app2   "docker-entrypoint.s…"   About a minute ago   Up About a minute   3000/tcp                            lb-challenge_weather-app2_1
2f15a482d742   lb-challenge_weather-app1   "docker-entrypoint.s…"   About a minute ago   Up About a minute   3000/tcp                            lb-challenge_weather-app1_1
ae7a55a08e68   lb-challenge_weather-app3   "docker-entrypoint.s…"   About a minute ago   Up About a minute   3000/tcp                            lb-challenge_weather-app3_1
[root@ip-10-0-1-62 lb-challenge]#




```

Docker swarm:

```sh

[root@ip-10-0-1-62 ~]# cat swarm-token.txt
Swarm initialized: current node (rnxfpfwpa5vm6yq0dg1ukfagp) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token <TOKEN-HERE> 10.0.1.62:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.

# on second host

[root@ip-10-0-1-70 ~]# docker swarm join --token $TOKEN 10.0.1.62:2377
This node joined a swarm as a worker.

# Back on fist host:

[root@ip-10-0-1-62 ~]# docker service create --name nginx-app --publish published=8080,target=80 --replicas=2 nginx
fe2ml50q1hxlypvj5zzj786br
overall progress: 2 out of 2 tasks
1/2: running   [==================================================>]
2/2: running   [==================================================>]
verify: Service converged

[root@ip-10-0-1-62 ~]# docker ps
CONTAINER ID   IMAGE                       COMMAND                  CREATED              STATUS          PORTS                               NAMES
39637051a806   nginx:latest                "/docker-entrypoint.…"   About a minute ago   Up 59 seconds   80/tcp                              nginx-app.1.tprcglk0oh6zaoltmdta5g3v7
e94ad40cdd19   nginx                       "/docker-entrypoint.…"   16 minutes ago       Up 16 minutes   0.0.0.0:80->80/tcp, :::80->80/tcp   lb-challenge_loadbalancer_1
e6931755b1e6   lb-challenge_weather-app2   "docker-entrypoint.s…"   16 minutes ago       Up 16 minutes   3000/tcp                            lb-challenge_weather-app2_1
2f15a482d742   lb-challenge_weather-app1   "docker-entrypoint.s…"   16 minutes ago       Up 16 minutes   3000/tcp                            lb-challenge_weather-app1_1
ae7a55a08e68   lb-challenge_weather-app3   "docker-entrypoint.s…"   16 minutes ago       Up 16 minutes   3000/tcp                            lb-challenge_weather-app3_1


[root@ip-10-0-1-70 ~]# docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS     NAMES
04a9ce18b5e0   nginx:latest   "/docker-entrypoint.…"   54 seconds ago   Up 49 seconds   80/tcp    nginx-app.2.wogoveuxsewdryon8avl2nmmv
[root@ip-10-0-1-70 ~]#

```

On second host public IP, 8080 we get default NGINX page