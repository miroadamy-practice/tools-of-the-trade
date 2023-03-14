# 02-01 Initializing Docker environment

Log from terminal:

```sh
-  
  ```
  / $ ssh cloud_user@18.232.61.174
  
  Host '18.232.61.174' is not in the trusted hosts file.
  (ssh-ed25519 fingerprint sha1!! 53:2b:40:b0:62:82:36:6b:33:36:67:cd:f9:91:d8:3b:be:d6:20:5b)
  Do you want to continue connecting? (y/n) y
  cloud_user@18.232.61.174's password:


  [cloud_user@ip-10-0-1-227 ~]$ sudo yum install -y yum-utils device-mapper-persistent-data lvm2
  [sudo] password for cloud_user:
  Loaded plugins: fastestmirror
  Loading mirror speeds from cached hostfile
   * base: download.cf.centos.org
   * extras: download.cf.centos.org
   * updates: download.cf.centos.org
  Package yum-utils-1.1.31-54.el7_8.noarch already installed and latest version
  Resolving Dependencies
  --> Running transaction check
  ---> Package device-mapper-persistent-data.x86_64 0:0.8.5-3.el7_9.2 will be installed
  --> Processing Dependency: libaio.so.1(LIBAIO_0.4)(64bit) for package: device-mapper-persistent-data-0.8.5-3.el7_9.2.x86_64
  --> Processing Dependency: libaio.so.1(LIBAIO_0.1)(64bit) for package: device-mapper-persistent-data-0.8.5-3.el7_9.2.x86_64
  --> Processing Dependency: libaio.so.1()(64bit) for package: device-mapper-persistent-data-0.8.5-3.el7_9.2.x86_64
  ---> Package lvm2.x86_64 7:2.02.187-6.el7_9.5 will be installed
  --> Processing Dependency: lvm2-libs = 7:2.02.187-6.el7_9.5 for package: 7:lvm2-2.02.187-6.el7_9.5.x86_64
  --> Processing Dependency: liblvm2app.so.2.2(Base)(64bit) for package: 7:lvm2-2.02.187-6.el7_9.5.x86_64
  --> Processing Dependency: libdevmapper-event.so.1.02(Base)(64bit) for package: 7:lvm2-2.02.187-6.el7_9.5.x86_64
  --> Processing Dependency: liblvm2app.so.2.2()(64bit) for package: 7:lvm2-2.02.187-6.el7_9.5.x86_64
  --> Processing Dependency: libdevmapper-event.so.1.02()(64bit) for package: 7:lvm2-2.02.187-6.el7_9.5.x86_64
  --> Running transaction check
  ---> Package device-mapper-event-libs.x86_64 7:1.02.170-6.el7_9.5 will be installed
  ---> Package libaio.x86_64 0:0.3.109-13.el7 will be installed
  ---> Package lvm2-libs.x86_64 7:2.02.187-6.el7_9.5 will be installed
  --> Processing Dependency: device-mapper-event = 7:1.02.170-6.el7_9.5 for package: 7:lvm2-libs-2.02.187-6.el7_9.5.x86_64
  --> Running transaction check
  ---> Package device-mapper-event.x86_64 7:1.02.170-6.el7_9.5 will be installed
  --> Finished Dependency Resolution
  
  Dependencies Resolved
  
  =============================================================================================================================================================
   Package                                            Arch                        Version                                   Repository                    Size
  =============================================================================================================================================================
  Installing:
   device-mapper-persistent-data                      x86_64                      0.8.5-3.el7_9.2                           updates                      423 k
   lvm2                                               x86_64                      7:2.02.187-6.el7_9.5                      updates                      1.3 M
  Installing for dependencies:
   device-mapper-event                                x86_64                      7:1.02.170-6.el7_9.5                      updates                      192 k
   device-mapper-event-libs                           x86_64                      7:1.02.170-6.el7_9.5                      updates                      192 k
   libaio                                             x86_64                      0.3.109-13.el7                            base                          24 k
   lvm2-libs                                          x86_64                      7:2.02.187-6.el7_9.5                      updates                      1.1 M
  
  Transaction Summary
  =============================================================================================================================================================
  Install  2 Packages (+4 Dependent packages)
  
  Total download size: 3.2 M
  Installed size: 8.1 M
  Downloading packages:
  (1/6): device-mapper-event-1.02.170-6.el7_9.5.x86_64.rpm                                                                              | 192 kB  00:00:00
  (2/6): device-mapper-persistent-data-0.8.5-3.el7_9.2.x86_64.rpm                                                                       | 423 kB  00:00:00
  (3/6): lvm2-2.02.187-6.el7_9.5.x86_64.rpm                                                                                             | 1.3 MB  00:00:00
  (4/6): device-mapper-event-libs-1.02.170-6.el7_9.5.x86_64.rpm                                                                         | 192 kB  00:00:00
  (5/6): lvm2-libs-2.02.187-6.el7_9.5.x86_64.rpm                                                                                        | 1.1 MB  00:00:00
  (6/6): libaio-0.3.109-13.el7.x86_64.rpm                                                                                               |  24 kB  00:00:00
  -------------------------------------------------------------------------------------------------------------------------------------------------------------
  Total                                                                                                                        6.9 MB/s | 3.2 MB  00:00:00
  Running transaction check
  Running transaction test
  Transaction test succeeded
  Running transaction
    Installing : 7:device-mapper-event-libs-1.02.170-6.el7_9.5.x86_64                                                                                      1/6
    Installing : libaio-0.3.109-13.el7.x86_64                                                                                                              2/6
    Installing : device-mapper-persistent-data-0.8.5-3.el7_9.2.x86_64                                                                                      3/6
    Installing : 7:device-mapper-event-1.02.170-6.el7_9.5.x86_64                                                                                           4/6
    Installing : 7:lvm2-libs-2.02.187-6.el7_9.5.x86_64                                                                                                     5/6
    Installing : 7:lvm2-2.02.187-6.el7_9.5.x86_64                                                                                                          6/6
    Verifying  : 7:device-mapper-event-1.02.170-6.el7_9.5.x86_64                                                                                           1/6
    Verifying  : 7:lvm2-libs-2.02.187-6.el7_9.5.x86_64                                                                                                     2/6
    Verifying  : device-mapper-persistent-data-0.8.5-3.el7_9.2.x86_64                                                                                      3/6
    Verifying  : libaio-0.3.109-13.el7.x86_64                                                                                                              4/6
    Verifying  : 7:lvm2-2.02.187-6.el7_9.5.x86_64                                                                                                          5/6
    Verifying  : 7:device-mapper-event-libs-1.02.170-6.el7_9.5.x86_64                                                                                      6/6
  
  Installed:
    device-mapper-persistent-data.x86_64 0:0.8.5-3.el7_9.2                                   lvm2.x86_64 7:2.02.187-6.el7_9.5
  
  Dependency Installed:
    device-mapper-event.x86_64 7:1.02.170-6.el7_9.5         device-mapper-event-libs.x86_64 7:1.02.170-6.el7_9.5         libaio.x86_64 0:0.3.109-13.el7
    lvm2-libs.x86_64 7:2.02.187-6.el7_9.5
  
  Complete!


  [cloud_user@ip-10-0-1-227 ~]$ sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
  Loaded plugins: fastestmirror
  adding repo from: https://download.docker.com/linux/centos/docker-ce.repo
  grabbing file https://download.docker.com/linux/centos/docker-ce.repo to /etc/yum.repos.d/docker-ce.repo
  repo saved to /etc/yum.repos.d/docker-ce.repo
  
  
  [cloud_user@ip-10-0-1-227 ~]$ sudo yum -y install docker-ce
  Loaded plugins: fastestmirror
  Loading mirror speeds from cached hostfile
   * base: download.cf.centos.org
   * extras: download.cf.centos.org
   * updates: download.cf.centos.org
  docker-ce-stable                                                                                                                      | 3.5 kB  00:00:00
  (1/2): docker-ce-stable/7/x86_64/updateinfo                                                                                           |   55 B  00:00:00
  (2/2): docker-ce-stable/7/x86_64/primary_db                                                                                           |  97 kB  00:00:00
  Resolving Dependencies
  --> Running transaction check
  ---> Package docker-ce.x86_64 3:23.0.1-1.el7 will be installed
  --> Processing Dependency: container-selinux >= 2:2.74 for package: 3:docker-ce-23.0.1-1.el7.x86_64
  --> Processing Dependency: containerd.io >= 1.6.4 for package: 3:docker-ce-23.0.1-1.el7.x86_64
  --> Processing Dependency: docker-ce-cli for package: 3:docker-ce-23.0.1-1.el7.x86_64
  --> Processing Dependency: docker-ce-rootless-extras for package: 3:docker-ce-23.0.1-1.el7.x86_64
  --> Running transaction check
  ---> Package container-selinux.noarch 2:2.119.2-1.911c772.el7_8 will be installed
  ---> Package containerd.io.x86_64 0:1.6.18-3.1.el7 will be installed
  ---> Package docker-ce-cli.x86_64 1:23.0.1-1.el7 will be installed
  --> Processing Dependency: docker-buildx-plugin for package: 1:docker-ce-cli-23.0.1-1.el7.x86_64
  --> Processing Dependency: docker-compose-plugin for package: 1:docker-ce-cli-23.0.1-1.el7.x86_64
  --> Processing Dependency: docker-scan-plugin(x86-64) for package: 1:docker-ce-cli-23.0.1-1.el7.x86_64
  ---> Package docker-ce-rootless-extras.x86_64 0:23.0.1-1.el7 will be installed
  --> Processing Dependency: fuse-overlayfs >= 0.7 for package: docker-ce-rootless-extras-23.0.1-1.el7.x86_64
  --> Processing Dependency: slirp4netns >= 0.4 for package: docker-ce-rootless-extras-23.0.1-1.el7.x86_64
  --> Running transaction check
  ---> Package docker-buildx-plugin.x86_64 0:0.10.2-1.el7 will be installed
  ---> Package docker-compose-plugin.x86_64 0:2.16.0-1.el7 will be installed
  ---> Package docker-scan-plugin.x86_64 0:0.23.0-3.el7 will be installed
  ---> Package fuse-overlayfs.x86_64 0:0.7.2-6.el7_8 will be installed
  --> Processing Dependency: libfuse3.so.3(FUSE_3.2)(64bit) for package: fuse-overlayfs-0.7.2-6.el7_8.x86_64
  --> Processing Dependency: libfuse3.so.3(FUSE_3.0)(64bit) for package: fuse-overlayfs-0.7.2-6.el7_8.x86_64
  --> Processing Dependency: libfuse3.so.3()(64bit) for package: fuse-overlayfs-0.7.2-6.el7_8.x86_64
  ---> Package slirp4netns.x86_64 0:0.4.3-4.el7_8 will be installed
  --> Running transaction check
  ---> Package fuse3-libs.x86_64 0:3.6.1-4.el7 will be installed
  --> Finished Dependency Resolution
  
  Dependencies Resolved
  
  =============================================================================================================================================================
   Package                                     Arch                     Version                                       Repository                          Size
  =============================================================================================================================================================
  Installing:
   docker-ce                                   x86_64                   3:23.0.1-1.el7                                docker-ce-stable                    23 M
  Installing for dependencies:
   container-selinux                           noarch                   2:2.119.2-1.911c772.el7_8                     extras                              40 k
   containerd.io                               x86_64                   1.6.18-3.1.el7                                docker-ce-stable                    34 M
   docker-buildx-plugin                        x86_64                   0.10.2-1.el7                                  docker-ce-stable                    12 M
   docker-ce-cli                               x86_64                   1:23.0.1-1.el7                                docker-ce-stable                    13 M
   docker-ce-rootless-extras                   x86_64                   23.0.1-1.el7                                  docker-ce-stable                   8.8 M
   docker-compose-plugin                       x86_64                   2.16.0-1.el7                                  docker-ce-stable                    11 M
   docker-scan-plugin                          x86_64                   0.23.0-3.el7                                  docker-ce-stable                   3.8 M
   fuse-overlayfs                              x86_64                   0.7.2-6.el7_8                                 extras                              54 k
   fuse3-libs                                  x86_64                   3.6.1-4.el7                                   extras                              82 k
   slirp4netns                                 x86_64                   0.4.3-4.el7_8                                 extras                              81 k
  
  Transaction Summary
  =============================================================================================================================================================
  Install  1 Package (+10 Dependent packages)
  
  Total download size: 106 M
  Installed size: 372 M
  Downloading packages:
  (1/11): container-selinux-2.119.2-1.911c772.el7_8.noarch.rpm                                                                          |  40 kB  00:00:00
  warning: /var/cache/yum/x86_64/7/docker-ce-stable/packages/containerd.io-1.6.18-3.1.el7.x86_64.rpm: Header V4 RSA/SHA512 Signature, key ID 621e9f35: NOKEY
  Public key for containerd.io-1.6.18-3.1.el7.x86_64.rpm is not installed
  (2/11): containerd.io-1.6.18-3.1.el7.x86_64.rpm                                                                                       |  34 MB  00:00:00
  (3/11): docker-buildx-plugin-0.10.2-1.el7.x86_64.rpm                                                                                  |  12 MB  00:00:00
  (4/11): docker-ce-23.0.1-1.el7.x86_64.rpm                                                                                             |  23 MB  00:00:00
  (5/11): docker-ce-cli-23.0.1-1.el7.x86_64.rpm                                                                                         |  13 MB  00:00:00
  (6/11): docker-ce-rootless-extras-23.0.1-1.el7.x86_64.rpm                                                                             | 8.8 MB  00:00:00
  (7/11): fuse-overlayfs-0.7.2-6.el7_8.x86_64.rpm                                                                                       |  54 kB  00:00:00
  (8/11): slirp4netns-0.4.3-4.el7_8.x86_64.rpm                                                                                          |  81 kB  00:00:00
  (9/11): docker-scan-plugin-0.23.0-3.el7.x86_64.rpm                                                                                    | 3.8 MB  00:00:00
  (10/11): docker-compose-plugin-2.16.0-1.el7.x86_64.rpm                                                                                |  11 MB  00:00:00
  (11/11): fuse3-libs-3.6.1-4.el7.x86_64.rpm                                                                                            |  82 kB  00:00:00
  -------------------------------------------------------------------------------------------------------------------------------------------------------------
  Total                                                                                                                        103 MB/s | 106 MB  00:00:01
  Retrieving key from https://download.docker.com/linux/centos/gpg
  Importing GPG key 0x621E9F35:
   Userid     : "Docker Release (CE rpm) <docker@docker.com>"
   Fingerprint: 060a 61c5 1b55 8a7f 742b 77aa c52f eb6b 621e 9f35
   From       : https://download.docker.com/linux/centos/gpg
  Running transaction check
  Running transaction test
  Transaction test succeeded
  Running transaction
    Installing : 2:container-selinux-2.119.2-1.911c772.el7_8.noarch                                                                                       1/11
    Installing : containerd.io-1.6.18-3.1.el7.x86_64                                                                                                      2/11
    Installing : docker-buildx-plugin-0.10.2-1.el7.x86_64                                                                                                 3/11
    Installing : fuse3-libs-3.6.1-4.el7.x86_64                                                                                                            4/11
    Installing : fuse-overlayfs-0.7.2-6.el7_8.x86_64                                                                                                      5/11
    Installing : slirp4netns-0.4.3-4.el7_8.x86_64                                                                                                         6/11
    Installing : docker-scan-plugin-0.23.0-3.el7.x86_64                                                                                                   7/11
    Installing : docker-compose-plugin-2.16.0-1.el7.x86_64                                                                                                8/11
    Installing : 1:docker-ce-cli-23.0.1-1.el7.x86_64                                                                                                      9/11
    Installing : docker-ce-rootless-extras-23.0.1-1.el7.x86_64                                                                                           10/11
    Installing : 3:docker-ce-23.0.1-1.el7.x86_64                                                                                                         11/11
    Verifying  : docker-compose-plugin-2.16.0-1.el7.x86_64                                                                                                1/11
    Verifying  : docker-scan-plugin-0.23.0-3.el7.x86_64                                                                                                   2/11
    Verifying  : fuse-overlayfs-0.7.2-6.el7_8.x86_64                                                                                                      3/11
    Verifying  : slirp4netns-0.4.3-4.el7_8.x86_64                                                                                                         4/11
    Verifying  : 2:container-selinux-2.119.2-1.911c772.el7_8.noarch                                                                                       5/11
    Verifying  : containerd.io-1.6.18-3.1.el7.x86_64                                                                                                      6/11
    Verifying  : docker-ce-rootless-extras-23.0.1-1.el7.x86_64                                                                                            7/11
    Verifying  : 3:docker-ce-23.0.1-1.el7.x86_64                                                                                                          8/11
    Verifying  : fuse3-libs-3.6.1-4.el7.x86_64                                                                                                            9/11
    Verifying  : docker-buildx-plugin-0.10.2-1.el7.x86_64                                                                                                10/11
    Verifying  : 1:docker-ce-cli-23.0.1-1.el7.x86_64                                                                                                     11/11
  
  Installed:
    docker-ce.x86_64 3:23.0.1-1.el7
  
  Dependency Installed:
    container-selinux.noarch 2:2.119.2-1.911c772.el7_8     containerd.io.x86_64 0:1.6.18-3.1.el7               docker-buildx-plugin.x86_64 0:0.10.2-1.el7
    docker-ce-cli.x86_64 1:23.0.1-1.el7                    docker-ce-rootless-extras.x86_64 0:23.0.1-1.el7     docker-compose-plugin.x86_64 0:2.16.0-1.el7
    docker-scan-plugin.x86_64 0:0.23.0-3.el7               fuse-overlayfs.x86_64 0:0.7.2-6.el7_8               fuse3-libs.x86_64 0:3.6.1-4.el7
    slirp4netns.x86_64 0:0.4.3-4.el7_8
  
  Complete!



  [cloud_user@ip-10-0-1-227 ~]$ sudo systemctl enable --now docker
  Created symlink from /etc/systemd/system/multi-user.target.wants/docker.service to /usr/lib/systemd/system/docker.service.


  [cloud_user@ip-10-0-1-227 ~]$ sudo usermod -aG docker cloud_user


  [cloud_user@ip-10-0-1-227 ~]$ docker run hello-world
  docker: permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Post "http://%2Fvar%2Frun%2Fdocker.sock/v1.24/containers/create": dial unix /var/run/docker.sock: connect: permission denied.
  See 'docker run --help'.
  
  [cloud_user@ip-10-0-1-227 ~]$ logout
  / $ ssh cloud_user@18.232.61.174
  cloud_user@18.232.61.174's password:
  
  
  [cloud_user@ip-10-0-1-227 ~]$ docker run hello-world

  
  Unable to find image 'hello-world:latest' locally
  latest: Pulling from library/hello-world
  2db29710123e: Pull complete
  Digest: sha256:63421b18c1443a9a85139225293fae7541fb40b7832d9deff80b6a9a75ce3604
  Status: Downloaded newer image for hello-world:latest
  
  Hello from Docker!
  This message shows that your installation appears to be working correctly.
  
  To generate this message, Docker took the following steps:
   1. The Docker client contacted the Docker daemon.
   2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
      (amd64)
   3. The Docker daemon created a new container from that image which runs the
      executable that produces the output you are currently reading.
   4. The Docker daemon streamed that output to the Docker client, which sent it
      to your terminal.
  
  To try something more ambitious, you can run an Ubuntu container with:
   $ docker run -it ubuntu bash
  
  Share images, automate workflows, and more with a free Docker ID:
   https://hub.docker.com/
  
  For more examples and ideas, visit:
   https://docs.docker.com/get-started/
  
  ```

```