# Build Services with Docker Compose

# Build Services with Docker Compose

## Introduction

For the last six months, the Acme Anvil Corporation has been migrating some of their bare metal infrastructure to Docker containers. Your team wants to find an easier way to deploy applications that consist of multiple containers and has decided to use Docker Compose. You have been tasked with setting up an internal blog so the team can write technical articles. This blog will consist of two services: a Ghost Blog service and a MySQL service. Both services will use volumes for persistent storage.

## Solution

Begin by logging in to the lab server using the credentials provided on the hands-on lab page:

`ssh cloud_user@PUBLIC_IP_ADDRESS`

Become the `root` user:

`sudo su -`

### Create a Ghost Blog and MySQL Service

1. Create a `docker-compose.yml` file in the root directory.
    
    `vi docker-compose.yml`
    
2. Add the following contents to it:
    
 ```yaml   
    version: '3'
    services:
      ghost:
        image: ghost:1-alpine
        container_name: ghost-blog
        restart: always
        ports:
          - 80:2368
        environment:
          database__client: mysql
          database__connection__host: mysql
          database__connection__user: root
          database__connection__password: P4sSw0rd0!
          database__connection__database: ghost
        volumes:
          - ghost-volume:/var/lib/ghost
        depends_on:
          - mysql
    
      mysql:
        image: mysql:5.7
        container_name: ghost-db
        restart: always
        environment:
          MYSQL_ROOT_PASSWORD: P4sSw0rd0!
        volumes:
          - mysql-volume:/var/lib/mysql
    
    volumes:
      ghost-volume:
      mysql-volume:
    
```


### Bring Up the Ghost Blog Service

1. Start up the Docker Compose service.
    
    `docker-compose up -d`


## LOG

```sh

cloud_user@ip-10-0-1-223 ~]$ ll
total 4
-rw-r--r--. 1 cloud_user cloud_user 1 Aug  9  2021 init_pass
[cloud_user@ip-10-0-1-223 ~]$ sudo su -
[sudo] password for cloud_user:
[root@ip-10-0-1-223 ~]#


[root@ip-10-0-1-223 ~]# vi docker-compose.yml
[root@ip-10-0-1-223 ~]# cat docker-compose.yml
version: '3'
services:
  ghost:
    image: ghost:1-alpine
    container_name: ghost-blog
    restart: always
    ports:
      - 80:2368
    environment:
      database__client: mysql
      database__connection__host: mysql
      database__connection__user: root
      database__connection__password: P4sSw0rd0!
      database__connection__database: ghost
    volumes:
      - ghost-volume:/var/lib/ghost
    depends_on:
      - mysql

  mysql:
    image: mysql:5.7
    container_name: ghost-db
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: P4sSw0rd0!
    volumes:
      - mysql-volume:/var/lib/mysql

volumes:
  ghost-volume:
  mysql-volume:
```

Run it

```sh
root@ip-10-0-1-223 ~]# docker-compose up -d
Creating network "root_default" with the default driver
Creating volume "root_ghost-volume" with default driver
Creating volume "root_mysql-volume" with default driver
Pulling mysql (mysql:5.7)...
5.7: Pulling from library/mysql
e83e8f2e82cc: Pull complete
0f23deb01b84: Pull complete
f5bda3b184ea: Pull complete
ed17edbc6604: Pull complete
33a94a6acfa7: Pull complete
3686cf92b89d: Pull complete
f81535a6a8bf: Pull complete
4bffb03ea5e2: Pull complete
49348ef8dcaa: Pull complete
509d665d0cf5: Pull complete
adc919b937fd: Pull complete
Digest: sha256:bf18020f32cc5d8f5e2add516d52fbf3afc3de431457076340e938596c528171
Status: Downloaded newer image for mysql:5.7
Pulling ghost (ghost:1-alpine)...
1-alpine: Pulling from library/ghost
aad63a933944: Pull complete
976f06839970: Pull complete
c29b7930f4f9: Pull complete
18316e90c190: Pull complete
7aba797547c3: Pull complete
ef529ab4d1ec: Pull complete
96e7ecd230d9: Pull complete
59586d3e4b30: Pull complete
089ba083e7d4: Pull complete
Digest: sha256:0a9957f8831db9fe6a87fe95217053939601fdcd1db047cb8b106f0ec4b7506b
Status: Downloaded newer image for ghost:1-alpine
Creating ghost-db ... done
Creating ghost-blog ... done


[root@ip-10-0-1-223 ~]# docker ps
CONTAINER ID   IMAGE            COMMAND                  CREATED          STATUS          PORTS                                   NAMES
4a3e8ad81f5c   ghost:1-alpine   "docker-entrypoint.s…"   46 seconds ago   Up 30 seconds   0.0.0.0:80->2368/tcp, :::80->2368/tcp   ghost-blog
dc94f4c33132   mysql:5.7        "docker-entrypoint.s…"   49 seconds ago   Up 45 seconds   3306/tcp, 33060/tcp                     ghost-db

[root@ip-10-0-1-223 ~]# docker-compose ps
   Name                 Command               State                  Ports
------------------------------------------------------------------------------------------
ghost-blog   docker-entrypoint.sh node  ...   Up      0.0.0.0:80->2368/tcp,:::80->2368/tcp
ghost-db     docker-entrypoint.sh mysqld      Up      3306/tcp, 33060/tcp


```