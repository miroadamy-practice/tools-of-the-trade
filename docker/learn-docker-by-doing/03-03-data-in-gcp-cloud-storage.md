# Storing Container Data in Google Cloud Storage

# Storing Container Data in Google Cloud Storage - A Cloud Guru

[Source](https://learn.acloud.guru/handson/6b09b8c1-4ced-44c3-b630-824752fcaf47/course/108f9d35-6966-4fb6-a6cd-7af135329bba "Permalink to Storing Container Data in Google Cloud Storage - A Cloud Guru")

## Introduction

Docker volumes is the preferred method of storing container data locally. Volume support is built directly into Docker, making it an easy tool to use for storage, as well as more portable. However, storing container data in Docker volumes still requires you to back up the data in those volumes on your own.

There is another option - storing your container data in the cloud. It's not a solution for every problem, but after this lab, you'll have another tool at your disposal.

This lab will show you how to mount a Cloud Storage bucket onto your local system as a directory. You will then mount that directory into your Docker container. We will use an `httpd` container, to serve the contents of that bucket as a webpage, but you can use it to share any common data between containers.

This hands-on lab will demonstrate how flexible Docker can be. You can make changes to your bucket and all of your containers using the Cloud Storage bucket will near-instantly have access to the content.

## Solution

Log in to the server using the credentials provided:

```
ssh cloud_user@<PUBLIC_IP_ADDRESS>
```

> **Note:** You may continue as `cloud_user` for the duration of the lab. You do *not* need to elevate to root user privileges to follow this lab guide.

### Configuration and Installation

1. Export the `projnum` variable:

```
export projnum=$(curl http://metadata.google.internal/computeMetadata/v1/project/numeric-project-id -sH "Metadata-Flavor: Google")
```
2. Verify that the variable was set successfully:

```
echo $projnum
```
3. Export the `BUCKET` variable:

```
export BUCKET="widgetfactory-${projnum}"
```

### Prepare the Cloud Storage Bucket

1. Using `gsutil`, create a new bucket:

```
gsutil mb -l us-central1 -c standard gs://$BUCKET
```
2. Verify that the `gcsfuse` repo is available on the server:

```
cat /etc/yum.repos.d/gcsfuse.repo
```
3. Install `gcsfuse` (Note: please wait a few minutes before continuing to allow the lab's startup scripts to release the yum lock).

```
sudo yum install -y gcsfuse
```
4. Update the `fuse.conf` file to allow the user to mount the bucket properly:

```
sudo sed -ri 's/# user_allow_other/user_allow_other/' /etc/fuse.conf
```
5. Configure the directories needed to mount the bucket:

```
sudo mkdir /mnt/widget-factory /tmp/gcs
```
6. Change ownership of the directories to the `cloud_user`:

```
sudo chown cloud_user: /mnt/widget-factory/ /tmp/gcs
```
7. Mount the bucket:

```
gcsfuse -o allow_other --temp-dir=/tmp/gcs $BUCKET /mnt/widget-factory/
```
8. Copy the website files into the bucket:

```
cp -r /home/cloud_user/widget-factory-inc/web/* /mnt/widget-factory/
```
9. List the contents of the bucket:

```
gsutil ls gs://$BUCKET
```

### Use the GCS Bucket in a Container

1. Mount the directory into the Docker container:

```
sudo docker run -d --name web1 --mount type=bind,source=/mnt/widget-factory,target=/usr/local/apache2/htdocs,readonly -p 80:80 httpd:2.4
```
2. Using a web browser, verify connectivity to the container:

```
<SERVER_PUBLIC_IP_ADDRESS>
```

## Conclusion

Congratulations — you've completed this hands-on lab!

# LOGS

```sh

/ $ ssh cloud_user@35.223.99.67

Host '35.223.99.67' is not in the trusted hosts file.
(ssh-ed25519 fingerprint sha1!! 59:b8:10:c3:63:6d:3f:5f:27:72:de:33:ca:ce:93:c5:09:a7:73:02)
Do you want to continue connecting? (y/n) y
cloud_user@35.223.99.67's password:
[cloud_user@widget-factory ~]$ export projnum=$(curl http://metadata.google.internal/computeMetadata/v1/project/numeric-project-id -sH "Metadata-Flavor: Google")
[cloud_user@widget-factory ~]$ echo $projnum
737876728370
[cloud_user@widget-factory ~]$ export BUCKET="widgetfactory-${projnum}"
[cloud_user@widget-factory ~]$ echo $BUCKET
widgetfactory-737876728370
[cloud_user@widget-factory ~]$ sudo yum install -y gcsfuse
[sudo] password for cloud_user:
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirror.wdc2.us.leaseweb.net
 * epel: ord.mirror.rackspace.com
 * extras: ftp.ussg.iu.edu
 * updates: mirror.lstn.net
Resolving Dependencies
--> Running transaction check
---> Package gcsfuse.x86_64 0:0.42.2-1 will be installed
--> Processing Dependency: fuse for package: gcsfuse-0.42.2-1.x86_64
--> Running transaction check
---> Package fuse.x86_64 0:2.9.2-11.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

=============================================================================================================================================================
 Package                             Arch                               Version                                    Repository                           Size
=============================================================================================================================================================
Installing:
 gcsfuse                             x86_64                             0.42.2-1                                   gcsfuse                              13 M
Installing for dependencies:
 fuse                                x86_64                             2.9.2-11.el7                               base                                 86 k

Transaction Summary
=============================================================================================================================================================
Install  1 Package (+1 Dependent package)

Total download size: 13 M
Installed size: 31 M
Downloading packages:
(1/2): fuse-2.9.2-11.el7.x86_64.rpm                                                                                                   |  86 kB  00:00:00
(2/2): 8d07caecab89223bd8c444491b4555c9e9a2c128ccd5cc6c12de6a42444449d5-gcsfuse-0.42.2-1.x86_64.rpm                                   |  13 MB  00:00:00
-------------------------------------------------------------------------------------------------------------------------------------------------------------
Total                                                                                                                         28 MB/s |  13 MB  00:00:00
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : fuse-2.9.2-11.el7.x86_64                                                                                                                  1/2
  Installing : gcsfuse-0.42.2-1.x86_64                                                                                                                   2/2
  Verifying  : gcsfuse-0.42.2-1.x86_64                                                                                                                   1/2
  Verifying  : fuse-2.9.2-11.el7.x86_64                                                                                                                  2/2

Installed:
  gcsfuse.x86_64 0:0.42.2-1

Dependency Installed:
  fuse.x86_64 0:2.9.2-11.el7

Complete!

[cloud_user@widget-factory ~]$ sudo sed -ri 's/# user_allow_other/user_allow_other/' /etc/fuse.conf

[cloud_user@widget-factory ~]$ cat /etc/fuse.conf
# mount_max = 1000
user_allow_other

[cloud_user@widget-factory ~]$ sudo mkdir /mnt/widget-factory /tmp/gcs

[cloud_user@widget-factory ~]$ sudo chown cloud_user: /mnt/widget-factory/ /tmp/gcs

[cloud_user@widget-factory ~]$ ll /mnt
total 0
drwxr-xr-x. 2 cloud_user cloud_user 6 Mar 26 19:49 widget-factory

[cloud_user@widget-factory ~]$ ll /tmp
total 0
drwxr-xr-x. 2 cloud_user cloud_user  6 Mar 26 19:49 gcs
drwx------. 3 root       root       17 Mar 26 14:28 systemd-private-aff92145259d4c3a898120e80b079b63-chronyd.service-LH6gBV

[cloud_user@widget-factory ~]$ ll /mnt/widget-factory/
total 0

[cloud_user@widget-factory ~]$ gcsfuse -o allow_other --temp-dir=/tmp/gcs $BUCKET /mnt/widget-factory/
2023/03/26 19:51:30.016496 Start gcsfuse/0.42.2 (Go version go1.19.5) for app "" using mount point: /mnt/widget-factory/
daemonize.Run: readFromProcess: sub-process: mountWithArgs: mountWithConn: fs.NewServer: create file system: SetUpBucket: Error in iterating through objects: storage: bucket doesn't exist

[cloud_user@widget-factory ~]$ ll /mnt/widget-factory/
total 0
[cloud_user@widget-factory ~]$ echo $BUCKET
widgetfactory-737876728370


=> ERROR: https://github.com/GoogleCloudPlatform/gcsfuse/issues/982
```