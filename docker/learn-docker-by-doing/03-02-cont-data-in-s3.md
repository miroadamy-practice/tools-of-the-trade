# Storing Container Data in AWS S3

# Storing Container Data in AWS S3 - A Cloud Guru

[Source](https://learn.acloud.guru/handson/244f54f0-1a70-460f-a6ed-8280cb167d76/course/108f9d35-6966-4fb6-a6cd-7af135329bba "Permalink to Storing Container Data in AWS S3 - A Cloud Guru")

## Introduction

Using Docker volumes is the preferred method of storing container data locally. Volume support is built directly into Docker, making it an easy tool to use for storage, as well as more portable. However, storing container data in Docker volumes still requires you to back up the data in those volumes on your own.

There is another option - storing your container data in the cloud. It's not a solution for every problem, but after this lab, you'll have another tool at your disposal.

This lab will show you how to mount an S3 bucket onto your local system as a directory. You will then mount that directory into your Docker container. We will use an `httpd` container to serve the contents of that bucket as a webpage, but you can use it to share any common data between containers.

This will demonstrate how flexible Docker can be. You can make changes to your bucket and all of your containers using the S3 bucket will near-instantly have access to the content.

## Solution

Log in to the server using the credentials provided:

```
ssh cloud_user@<PUBLIC_IP_ADDRESS>
```

### Configuration and Installation

1. Install the `awscli`, while checking if there are any versions currently installed, and not stopping any user processes:

```
pip install --upgrade --user awscli
```
2. Configure the CLI:

```
aws configure
```
3. Enter the following:
  * *AWS Access Key ID:* **\<ACCESS\_KEY\_ID\>**
  * *AWS Secret Access Key:* **\<SECRET\_ACCESS\_KEY\>**
  * *Default region name:* **us-east-1**
  * *Default output format:* **json**
4. Copy the CLI configuration to the `root` user:

```
sudo cp -r ~/.aws /root
```
5. Install the `s3fs` package:

```
sudo yum install s3fs-fuse -y
```

### Prepare the Bucket

1. Create a mount point for the s3 bucket:

```
sudo mkdir /mnt/widget-factory
```
2. Export the bucket name:

```
export BUCKET=<S3_BUCKET_NAME>
```
3. Mount the S3 bucket:

```
sudo s3fs $BUCKET /mnt/widget-factory -o allow_other -o default_acl=public-read -o use_cache=/tmp/s3fs
```
4. Verify that the bucket was mounted successfully:

```
ll /mnt/widget-factory
```
5. Copy the website files to the s3 bucket:

```
cp -r ~/widget-factory-inc/web/* /mnt/widget-factory
```
6. Verify the files are in the folder:

```
ll /mnt/widget-factory
```
7. Verify the files are in the s3 bucket:

```
aws s3 ls s3://$BUCKET
```

### Use the S3 Bucket Files in a Docker Container

1. Run an `httpd` container using the S3 bucket:

```
docker run -d --name web1 -p 80:80 --mount type=bind,source=/mnt/widget-factory,target=/usr/local/apache2/htdocs,readonly httpd:2.4
```
2. In a web browser, verify connectivity to the container:

```
<SERVER_PUBLIC_IP_ADDRESS>
```
3. Check the bucket cache:
4. Change to the `/mnt/widget-factory/` directory:

```
cd /mnt/widget-factory
```
5. Create a new page within the bucket:

```
cp index.html newPage.html
```
6. In a web browser, verify that the new page is accessible:

```
<SERVER_PUBLIC_IP_ADDRESS>/newPage.html
```
7. Verify that the page was added to the bucket:

```
aws s3 ls $BUCKET
```

## Conclusion

Congratulations — you've completed this hands-on lab!

# LOGS

```sh
 $ ssh cloud_user@3.235.51.240

Host '3.235.51.240' is not in the trusted hosts file.
(ssh-ed25519 fingerprint sha1!! 1c:aa:62:67:d5:58:dd:98:1f:ff:c1:7f:42:8b:95:be:c7:1d:be:00)
Do you want to continue connecting? (y/n) y
cloud_user@3.235.51.240's password:
Activate the web console with: systemctl enable --now cockpit.socket

Last login: Sun Mar 26 17:47:08 2023
[cloud_user@ip-10-0-1-93 ~]$ pip install --upgrade --user awscli
Collecting awscli
  Downloading https://files.pythonhosted.org/packages/6c/48/8cf68d31f3f8606904acc4d98965a2c020acd5e1b9a821b8a7fafdbbc81a/awscli-1.24.10-py3-none-any.whl (3.9MB)
    100% |████████████████████████████████| 3.9MB 383kB/s
Collecting s3transfer<0.6.0,>=0.5.0 (from awscli)
  Downloading https://files.pythonhosted.org/packages/7b/9c/f51775ebe7df5a7aa4e7c79ed671bde94e154bd968aca8d65bb24aba0c8c/s3transfer-0.5.2-py3-none-any.whl (79kB)
    100% |████████████████████████████████| 81kB 10.8MB/s
Collecting docutils<0.17,>=0.10 (from awscli)
  Downloading https://files.pythonhosted.org/packages/81/44/8a15e45ffa96e6cf82956dd8d7af9e666357e16b0d93b253903475ee947f/docutils-0.16-py2.py3-none-any.whl (548kB)
    100% |████████████████████████████████| 552kB 2.5MB/s
Collecting botocore==1.26.10 (from awscli)
  Downloading https://files.pythonhosted.org/packages/09/b8/794e0bd260198538ded90c26b353ddb632eab01950d4e7e2e2b8ee510d12/botocore-1.26.10-py3-none-any.whl (8.8MB)
    100% |████████████████████████████████| 8.8MB 157kB/s
Collecting colorama<0.4.5,>=0.2.5 (from awscli)
  Downloading https://files.pythonhosted.org/packages/44/98/5b86278fbbf250d239ae0ecb724f8572af1c91f4a11edf4d36a206189440/colorama-0.4.4-py2.py3-none-any.whl
Collecting PyYAML<5.5,>=3.10 (from awscli)
  Downloading https://files.pythonhosted.org/packages/7a/5b/bc0b5ab38247bba158504a410112b6c03f153c652734ece1849749e5f518/PyYAML-5.4.1-cp36-cp36m-manylinux1_x86_64.whl (640kB)
    100% |████████████████████████████████| 645kB 2.1MB/s
Collecting rsa<4.8,>=3.1.2 (from awscli)
  Downloading https://files.pythonhosted.org/packages/e9/93/0c0f002031f18b53af7a6166103c02b9c0667be528944137cc954ec921b3/rsa-4.7.2-py3-none-any.whl
Collecting urllib3<1.27,>=1.25.4 (from botocore==1.26.10->awscli)
  Downloading https://files.pythonhosted.org/packages/7b/f5/890a0baca17a61c1f92f72b81d3c31523c99bec609e60c292ea55b387ae8/urllib3-1.26.15-py2.py3-none-any.whl (140kB)
    100% |████████████████████████████████| 143kB 8.6MB/s
Collecting jmespath<2.0.0,>=0.7.1 (from botocore==1.26.10->awscli)
  Downloading https://files.pythonhosted.org/packages/07/cb/5f001272b6faeb23c1c9e0acc04d48eaaf5c862c17709d20e3469c6e0139/jmespath-0.10.0-py2.py3-none-any.whl
Collecting python-dateutil<3.0.0,>=2.1 (from botocore==1.26.10->awscli)
  Downloading https://files.pythonhosted.org/packages/36/7a/87837f39d0296e723bb9b62bbb257d0355c7f6128853c78955f57342a56d/python_dateutil-2.8.2-py2.py3-none-any.whl (247kB)
    100% |████████████████████████████████| 256kB 5.2MB/s
Collecting pyasn1>=0.1.3 (from rsa<4.8,>=3.1.2->awscli)
  Downloading https://files.pythonhosted.org/packages/62/1e/a94a8d635fa3ce4cfc7f506003548d0a2447ae76fd5ca53932970fe3053f/pyasn1-0.4.8-py2.py3-none-any.whl (77kB)
    100% |████████████████████████████████| 81kB 11.3MB/s
Collecting six>=1.5 (from python-dateutil<3.0.0,>=2.1->botocore==1.26.10->awscli)
  Downloading https://files.pythonhosted.org/packages/d9/5a/e7c31adbe875f2abbb91bd84cf2dc52d792b5a01506781dbcf25c91daf11/six-1.16.0-py2.py3-none-any.whl
Installing collected packages: urllib3, jmespath, six, python-dateutil, botocore, s3transfer, docutils, colorama, PyYAML, pyasn1, rsa, awscli
Successfully installed PyYAML-5.4.1 awscli-1.24.10 botocore-1.26.10 colorama-0.4.4 docutils-0.16 jmespath-0.10.0 pyasn1-0.4.8 python-dateutil-2.8.2 rsa-4.7.2 s3transfer-0.5.2 six-1.16.0 urllib3-1.26.15

[cloud_user@ip-10-0-1-93 ~]$

[cloud_user@ip-10-0-1-93 ~]$ aws configure
AWS Access Key ID [None]: AKIAX57PXJNTYJKUCLO6
AWS Secret Access Key [None]: <......>
Default region name [None]: us-east-1
Default output format [None]: json

[cloud_user@ip-10-0-1-93 ~]$ sudo cp -r ~/.aws /root
[sudo] password for cloud_user:
[cloud_user@ip-10-0-1-93 ~]$ sudo yum install s3fs-fuse -y
Last metadata expiration check: 0:52:57 ago on Sun 26 Mar 2023 06:35:41 PM UTC.
Dependencies resolved.
=============================================================================================================================================================
 Package                               Architecture                       Version                                   Repository                          Size
=============================================================================================================================================================
Installing:
 s3fs-fuse                             x86_64                             1.91-1.el8                                epel                               272 k
Installing dependencies:
 fuse                                  x86_64                             2.9.7-16.el8                              baseos                              83 k
 mailcap                               noarch                             2.1.48-3.el8                              baseos                              39 k

Transaction Summary
=============================================================================================================================================================
Install  3 Packages

Total download size: 394 k
Installed size: 922 k
Downloading Packages:
(1/3): fuse-2.9.7-16.el8.x86_64.rpm                                                                                          2.4 MB/s |  83 kB     00:00
(2/3): mailcap-2.1.48-3.el8.noarch.rpm                                                                                       1.1 MB/s |  39 kB     00:00
(3/3): s3fs-fuse-1.91-1.el8.x86_64.rpm                                                                                       2.3 MB/s | 272 kB     00:00
-------------------------------------------------------------------------------------------------------------------------------------------------------------
Total                                                                                                                        1.0 MB/s | 394 kB     00:00
Extra Packages for Enterprise Linux 8 - x86_64                                                                               1.6 MB/s | 1.6 kB     00:00
Importing GPG key 0x2F86D6A1:
 Userid     : "Fedora EPEL (8) <epel@fedoraproject.org>"
 Fingerprint: 94E2 79EB 8D8F 25B2 1810 ADF1 21EA 45AB 2F86 D6A1
 From       : /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-8
Key imported successfully
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                                                                                                     1/1
  Installing       : mailcap-2.1.48-3.el8.noarch                                                                                                         1/3
  Installing       : fuse-2.9.7-16.el8.x86_64                                                                                                            2/3
  Installing       : s3fs-fuse-1.91-1.el8.x86_64                                                                                                         3/3
  Running scriptlet: s3fs-fuse-1.91-1.el8.x86_64                                                                                                         3/3
  Verifying        : fuse-2.9.7-16.el8.x86_64                                                                                                            1/3
  Verifying        : mailcap-2.1.48-3.el8.noarch                                                                                                         2/3
  Verifying        : s3fs-fuse-1.91-1.el8.x86_64                                                                                                         3/3

Installed:
  fuse-2.9.7-16.el8.x86_64                          mailcap-2.1.48-3.el8.noarch                          s3fs-fuse-1.91-1.el8.x86_64

Complete!
[cloud_user@ip-10-0-1-93 ~]$

[cloud_user@ip-10-0-1-93 ~]$ sudo mkdir /mnt/widget-factory

[cloud_user@ip-10-0-1-93 ~]$ export BUCKET=widgetfactory-56b39ed0
[cloud_user@ip-10-0-1-93 ~]$ sudo s3fs $BUCKET /mnt/widget-factory -o allow_other -o default_acl=public-read -o use_cache=/tmp/s3fs

[cloud_user@ip-10-0-1-93 ~]$ ll /mnt/widget-factory
total 0

[cloud_user@ip-10-0-1-93 ~]$ cp -r ~/widget-factory-inc/web/* /mnt/widget-factory

[cloud_user@ip-10-0-1-93 ~]$ ll /mnt/widget-factory
total 13
drwxrwxr-x. 1 cloud_user cloud_user    0 Mar 26 19:30 img
-rw-rw-r--. 1 cloud_user cloud_user 3059 Mar 26 19:30 index.html
-rw-rw-r--. 1 cloud_user cloud_user 2910 Mar 26 19:30 quote.html
-rw-rw-r--. 1 cloud_user cloud_user 2611 Mar 26 19:30 support.html
-rw-rw-r--. 1 cloud_user cloud_user 2645 Mar 26 19:30 widgets.html
[cloud_user@ip-10-0-1-93 ~]$ aws s3 ls
2023-03-26 17:40:51 widgetfactory-56b39ed0

[cloud_user@ip-10-0-1-93 ~]$ aws s3 ls widgetfactory-56b39ed0
                           PRE img/
2023-03-26 19:30:35       3059 index.html
2023-03-26 19:30:35       2910 quote.html
2023-03-26 19:30:35       2611 support.html
2023-03-26 19:30:35       2645 widgets.html

[cloud_user@ip-10-0-1-93 ~]$ aws s3 ls s3://$BUCKET
                           PRE img/
2023-03-26 19:30:35       3059 index.html
2023-03-26 19:30:35       2910 quote.html
2023-03-26 19:30:35       2611 support.html
2023-03-26 19:30:35       2645 widgets.html

[cloud_user@ip-10-0-1-93 ~]$ docker run -d --name web1 -p 80:80 --mount type=bind,source=/mnt/widget-factory,target=/usr/local/apache2/htdocs,readonly httpd:2.4
a6dd5add636801c0432ca5db318f594490cfff0dfefa7a8c00294cd24d2a3cd7

[cloud_user@ip-10-0-1-93 ~]$ docker ps
CONTAINER ID   IMAGE       COMMAND              CREATED         STATUS         PORTS                               NAMES
a6dd5add6368   httpd:2.4   "httpd-foreground"   4 seconds ago   Up 3 seconds   0.0.0.0:80->80/tcp, :::80->80/tcp   web1

[cloud_user@ip-10-0-1-93 ~]$ cd /mnt/widget-factory
[cloud_user@ip-10-0-1-93 widget-factory]$ cp index.html newPage.html
[cloud_user@ip-10-0-1-93 widget-factory]$ curl http://3.235.51.240/newPage.html
<!doctype html>
<html lang="en">
<head>
        <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
        <script src="https://code.jquery.com/jquery-3.4.1.slim.min.js" integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n" crossorigin="anonymous"></script>
        <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js" integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous"></script>
        <title>Widget Factory, Inc</title>
        <style>
        body {
          background: url('https://source.unsplash.com/SLIFI67jv5k') no-repeat center center fixed;

          -webkit-background-size: cover;
          -moz-background-size: cover;
          background-size: cover;
          -o-background-size: cover;
        }
        </style>
</head>
<body>
<div id="header">
        <h2 class="font-weight-bold text-center bg-dark text-light mb-0">Widget Factory, Inc</h2>
</div>

<nav class="navbar navbar-expand-sm bg-dark navbar-dark sticky-top mb-3">
        <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navlinks">
                <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navlinks">
        <ul class="navbar-nav">
                <li class="nav-item">
                        <a class="nav-link active" href="/">Home</a>
                </li>
                <li class="nav-item">
                        <a class="nav-link" href="/widgets.html">Widgets</a>
                </li>
                <li class="nav-item">
                        <a class="nav-link" href="/quote.html">Free quote</a>
                </li>
                <li class="nav-item">
                        <a class="nav-link" href="/support.html">Support</a>
                </li>
        </ul>
        </div>
</nav>

<div class="container">
        <p class="bg-light">
                At Widget Factory, Inc, we produce quality, affordable widgets. Large widgets? We make them. Small widgets? We make those too. Medium widgets? Of course we make medium widgets! We manufacture widgets of all shapes and sizes.
        </p>
        <p class="bg-light">
                You're probably thinking that making widgets of all shapes and sizes is standard for the industry, and you're right. We also have colors! Get your red widgets, green widgets, and yes, even blue widgets all right here. Want another color? We will happily mix any RGB color! Do you need a widget to match the color of an existing product, but don't know what color it is? We can match that for you.
        </p>
        <p class="bg-light">
                What really sets us apart is that our widgets are affordable. Our prices can't be beat! If you're in the market for a widget, or need a widget brought to market, look no further than Widget Factory, Inc. Request a <a href="/quote.html">free quote</a> today!
        </p>
</div>
</body>
</html>


```


