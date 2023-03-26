# Storing Container Data In Docker Volumes

## ABOUT THIS LAB

Storing data within a container image is one option for automating a container with data, but it requires a copy of the data to be in each container you run. For static files, this can be a waste of resources. Each file might not amount to more than a few megabytes, but once the containers are scaled up to handle a production load, that few megabytes can turn into gigabytes of waste. Instead, you can store one copy of the static files in a Docker volume for easy sharing between containers. In this lab, you will learn how Docker volumes interact with containers. You will do this by creating new volumes and attaching them to containers. You'll then clean up space left by anonymous volumes created automatically by the containers. Finally, you'll learn about backup strategies for your volumes.

```sh

[cloud_user@ip-10-0-1-31 ~]$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
httpd        2.4       192d41583429   3 days ago    145MB
bash         latest    4f9df00fa318   6 weeks ago   12.3MB
postgres     12.1      cf879a45faaa   3 years ago   394MB

[cloud_user@ip-10-0-1-31 ~]$ docker run -d --name db1 postgres:12.1
cc2851bfb31d2f48b8be5fe244ede15e0e7b85f6959b74a2ac68d1e9df6e5e60

[cloud_user@ip-10-0-1-31 ~]$ docker run -d --name db2 postgres:12.1
cbeca4008352354d2da4c489c7baf1483bd0344fdebec3ed04028539bc6158b6

[cloud_user@ip-10-0-1-31 ~]$ docker ps
CONTAINER ID   IMAGE           COMMAND                  CREATED          STATUS          PORTS      NAMES
cbeca4008352   postgres:12.1   "docker-entrypoint.s…"   8 seconds ago    Up 8 seconds    5432/tcp   db2
cc2851bfb31d   postgres:12.1   "docker-entrypoint.s…"   23 seconds ago   Up 22 seconds   5432/tcp   db1

[cloud_user@ip-10-0-1-31 ~]$ docker volume ls
DRIVER    VOLUME NAME
local     4ce83cb38f1408c5065b9c6569cee8f21f5b60b37c9e5eb0a43bba2f3910c6ce
local     07ad383e9bdf30fe35aa58bef491f13dc10f62553a775a981a82b3e899bbd66e

# Anonymous volumes
[cloud_user@ip-10-0-1-31 ~]$ docker inspect db1 -f '{{ json .Mounts }}' | python -m json.tool
[
    {
        "Destination": "/var/lib/postgresql/data",
        "Driver": "local",
        "Mode": "",
        "Name": "4ce83cb38f1408c5065b9c6569cee8f21f5b60b37c9e5eb0a43bba2f3910c6ce",
        "Propagation": "",
        "RW": true,
        "Source": "/var/lib/docker/volumes/4ce83cb38f1408c5065b9c6569cee8f21f5b60b37c9e5eb0a43bba2f3910c6ce/_data",
        "Type": "volume"
    }
]


[cloud_user@ip-10-0-1-31 ~]$ docker inspect db2 -f '{{ json .Mounts }}' | python -m json.tool
[
    {
        "Destination": "/var/lib/postgresql/data",
        "Driver": "local",
        "Mode": "",
        "Name": "07ad383e9bdf30fe35aa58bef491f13dc10f62553a775a981a82b3e899bbd66e",
        "Propagation": "",
        "RW": true,
        "Source": "/var/lib/docker/volumes/07ad383e9bdf30fe35aa58bef491f13dc10f62553a775a981a82b3e899bbd66e/_data",
        "Type": "volume"
    }
]

[cloud_user@ip-10-0-1-31 ~]$ docker run -d --rm --name dbTmp postgres:12.1
1845262a686e69ab41ec4a49de1497915b6a872c5d8ea1a712e34dc94055f8d3

[cloud_user@ip-10-0-1-31 ~]$ docker ps
CONTAINER ID   IMAGE           COMMAND                  CREATED         STATUS         PORTS      NAMES
1845262a686e   postgres:12.1   "docker-entrypoint.s…"   4 seconds ago   Up 3 seconds   5432/tcp   dbTmp
cbeca4008352   postgres:12.1   "docker-entrypoint.s…"   4 minutes ago   Up 4 minutes   5432/tcp   db2
cc2851bfb31d   postgres:12.1   "docker-entrypoint.s…"   4 minutes ago   Up 4 minutes   5432/tcp   db1

[cloud_user@ip-10-0-1-31 ~]$ docker volume ls
DRIVER    VOLUME NAME
local     4ce83cb38f1408c5065b9c6569cee8f21f5b60b37c9e5eb0a43bba2f3910c6ce
local     07ad383e9bdf30fe35aa58bef491f13dc10f62553a775a981a82b3e899bbd66e
local     a2c336ae7e56d526631f47f528a51bf9e87c57b71e523d7967cb2a92a60d3235

[cloud_user@ip-10-0-1-31 ~]$ docker stop db2 dbTmp
db2
dbTmp

[cloud_user@ip-10-0-1-31 ~]$ docker ps -a
CONTAINER ID   IMAGE           COMMAND                  CREATED         STATUS                     PORTS      NAMES
cbeca4008352   postgres:12.1   "docker-entrypoint.s…"   5 minutes ago   Exited (0) 7 seconds ago              db2
cc2851bfb31d   postgres:12.1   "docker-entrypoint.s…"   5 minutes ago   Up 5 minutes               5432/tcp   db1

[cloud_user@ip-10-0-1-31 ~]$ docker volume ls
DRIVER    VOLUME NAME
local     4ce83cb38f1408c5065b9c6569cee8f21f5b60b37c9e5eb0a43bba2f3910c6ce
local     07ad383e9bdf30fe35aa58bef491f13dc10f62553a775a981a82b3e899bbd66e

```

Create a docker volume

```sh

[cloud_user@ip-10-0-1-31 ~]$ docker volume create website
website

[cloud_user@ip-10-0-1-31 ~]$ docker volume ls
DRIVER    VOLUME NAME
local     4ce83cb38f1408c5065b9c6569cee8f21f5b60b37c9e5eb0a43bba2f3910c6ce
local     07ad383e9bdf30fe35aa58bef491f13dc10f62553a775a981a82b3e899bbd66e
local     website

[cloud_user@ip-10-0-1-31 ~]$ sudo cp -r /home/cloud_user/widget-factory-inc/web/* /var/lib/docker/volumes/website/_data/
[sudo] password for cloud_user:

[cloud_user@ip-10-0-1-31 ~]$ sudo ls -l /var/lib/docker/volumes/website/_data/
total 16
drwxr-xr-x. 2 root root   76 Mar 26 12:46 img
-rw-r--r--. 1 root root 3059 Mar 26 12:46 index.html
-rw-r--r--. 1 root root 2910 Mar 26 12:46 quote.html
-rw-r--r--. 1 root root 2611 Mar 26 12:46 support.html
-rw-r--r--. 1 root root 2645 Mar 26 12:46 widgets.html

[cloud_user@ip-10-0-1-31 ~]$ docker run -d --name web1 -p 80:80 -v website:/usr/local/apache2/htdocs:ro httpd:2.4
c97e0778af14e71d0f71dc34612bba657654ca0ca095714fa9ef70d73c76f856

[cloud_user@ip-10-0-1-31 ~]$ docker ps
CONTAINER ID   IMAGE           COMMAND                  CREATED         STATUS         PORTS                               NAMES
c97e0778af14   httpd:2.4       "httpd-foreground"       9 seconds ago   Up 8 seconds   0.0.0.0:80->80/tcp, :::80->80/tcp   web1
cc2851bfb31d   postgres:12.1   "docker-entrypoint.s…"   8 minutes ago   Up 8 minutes   5432/tcp                            db1

[cloud_user@ip-10-0-1-31 ~]$ docker run -d --name webTmp --rm -v website:/usr/local/apache2/htdocs:ro httpd:2.4
48d2ea5c7a4605f98036a8ce41862ff4b5820d578edcd5b10e77cbd6d6d0a6e8

[cloud_user@ip-10-0-1-31 ~]$ docker ps
CONTAINER ID   IMAGE           COMMAND                  CREATED          STATUS          PORTS                               NAMES
48d2ea5c7a46   httpd:2.4       "httpd-foreground"       7 seconds ago    Up 5 seconds    80/tcp                              webTmp
c97e0778af14   httpd:2.4       "httpd-foreground"       49 seconds ago   Up 48 seconds   0.0.0.0:80->80/tcp, :::80->80/tcp   web1
cc2851bfb31d   postgres:12.1   "docker-entrypoint.s…"   9 minutes ago    Up 9 minutes    5432/tcp                            db1

[cloud_user@ip-10-0-1-31 ~]$ docker stop webTmp
webTmp

[cloud_user@ip-10-0-1-31 ~]$ docker ps -a
CONTAINER ID   IMAGE           COMMAND                  CREATED          STATUS                     PORTS                               NAMES
c97e0778af14   httpd:2.4       "httpd-foreground"       2 minutes ago    Up 2 minutes               0.0.0.0:80->80/tcp, :::80->80/tcp   web1
cbeca4008352   postgres:12.1   "docker-entrypoint.s…"   10 minutes ago   Exited (0) 5 minutes ago                                       db2
cc2851bfb31d   postgres:12.1   "docker-entrypoint.s…"   10 minutes ago   Up 10 minutes              5432/tcp                            db1
[cloud_user@ip-10-0-1-31 ~]$

```

Prune nefunguje ??

```sh
[cloud_user@ip-10-0-1-31 ~]$ docker volume ls
DRIVER    VOLUME NAME
local     4ce83cb38f1408c5065b9c6569cee8f21f5b60b37c9e5eb0a43bba2f3910c6ce
local     07ad383e9bdf30fe35aa58bef491f13dc10f62553a775a981a82b3e899bbd66e
local     website
[cloud_user@ip-10-0-1-31 ~]$ docker volume prune
WARNING! This will remove all local volumes not used by at least one container.
Are you sure you want to continue? [y/N] Y
Total reclaimed space: 0B
[cloud_user@ip-10-0-1-31 ~]$ docker volume ls
DRIVER    VOLUME NAME
local     4ce83cb38f1408c5065b9c6569cee8f21f5b60b37c9e5eb0a43bba2f3910c6ce
local     07ad383e9bdf30fe35aa58bef491f13dc10f62553a775a981a82b3e899bbd66e
local     website
[cloud_user@ip-10-0-1-31 ~]$ docker ps -a
CONTAINER ID   IMAGE           COMMAND                  CREATED          STATUS                     PORTS                               NAMES
c97e0778af14   httpd:2.4       "httpd-foreground"       4 minutes ago    Up 4 minutes               0.0.0.0:80->80/tcp, :::80->80/tcp   web1
cbeca4008352   postgres:12.1   "docker-entrypoint.s…"   12 minutes ago   Exited (0) 6 minutes ago                                       db2
cc2851bfb31d   postgres:12.1   "docker-entrypoint.s…"   12 minutes ago   Up 12 minutes              5432/tcp                            db1
[cloud_user@ip-10-0-1-31 ~]$ docker rm db2
db2
[cloud_user@ip-10-0-1-31 ~]$ docker volume prune
WARNING! This will remove all local volumes not used by at least one container.
Are you sure you want to continue? [y/N] y
Total reclaimed space: 0B
[cloud_user@ip-10-0-1-31 ~]$ docker volume ls
DRIVER    VOLUME NAME
local     4ce83cb38f1408c5065b9c6569cee8f21f5b60b37c9e5eb0a43bba2f3910c6ce
local     07ad383e9bdf30fe35aa58bef491f13dc10f62553a775a981a82b3e899bbd66e
local     website
[cloud_user@ip-10-0-1-31 ~]$ docker ps -a
CONTAINER ID   IMAGE           COMMAND                  CREATED          STATUS          PORTS                               NAMES
c97e0778af14   httpd:2.4       "httpd-foreground"       5 minutes ago    Up 5 minutes    0.0.0.0:80->80/tcp, :::80->80/tcp   web1
cc2851bfb31d   postgres:12.1   "docker-entrypoint.s…"   13 minutes ago   Up 13 minutes   5432/tcp                            db1
```

Since docker 23, this is the way

```sh
[cloud_user@ip-10-0-1-31 ~]$ docker volume prune --filter all=1
WARNING! This will remove all local volumes not used by at least one container.
Are you sure you want to continue? [y/N] y
Deleted Volumes:
07ad383e9bdf30fe35aa58bef491f13dc10f62553a775a981a82b3e899bbd66e

Total reclaimed space: 41.43MB

[cloud_user@ip-10-0-1-31 ~]$ docker volume ls
DRIVER    VOLUME NAME
local     4ce83cb38f1408c5065b9c6569cee8f21f5b60b37c9e5eb0a43bba2f3910c6ce
local     website

```

## Backup and restore

```sh

[cloud_user@ip-10-0-1-31 ~]$ sudo -i
[sudo] password for cloud_user:
[root@ip-10-0-1-31 ~]# docker volume inspect website
[
    {
        "CreatedAt": "2023-03-26T12:46:14-04:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/website/_data",
        "Name": "website",
        "Options": null,
        "Scope": "local"
    }
]
[root@ip-10-0-1-31 ~]# tar czf /tmp/website_$(date +%Y-%m-%d-%H%M).tgz -C /var/lib/docker/volumes/website/_data .

[root@ip-10-0-1-31 ~]# ll
total 4
-rw-------. 1 root root 1524 Feb  3  2017 anaconda-ks.cfg

[root@ip-10-0-1-31 ~]# ll /tmp
total 1200
-rw-r--r--. 1 root root 1212535 Jan 18  2022 aws-cfn-bootstrap-latest.amzn1.noarch.rpm
drwx------. 3 root root      17 Mar 26 12:29 systemd-private-7114cb6477e8429eb5f03211aa9d4805-chronyd.service-M7DSSK
-rw-r--r--. 1 root root   11364 Mar 26 13:03 website_2023-03-26-1303.tgz

[root@ip-10-0-1-31 ~]# tar tf /tmp/website_2023-03-26-1303.tgz
./
./img/
./img/LargeWidget.png
./img/MediumWidget.png
./img/SmallWidget.png
./index.html
./quote.html
./support.html
./widgets.html
```