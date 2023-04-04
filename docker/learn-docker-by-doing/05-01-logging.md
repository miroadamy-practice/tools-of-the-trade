## Container Logging

### Introduction

In this lab, we are going to configure syslog on a Docker instance, configure Docker to use syslog instead of the JSON logging driver, and spin up two containers to test our configuration.

Let's get started. Open your terminal application, and log in to the live environment using the credentials provided on the lab instructions page.

`ssh cloud_user@<PUBLIC_IP>`

Next, elevate privileges to root.

`sudo su -`

## Configure Docker to Use Syslog

Open the `rsyslog.conf` file.

`vim /etc/rsyslog.conf`

In the file editor, uncomment the two lines under `Provides UDP syslog reception` by removing `#`.

`$ModLoad imudp
$UDPServerRun 514`

Then, start the syslog service.

`systemctl start rsyslog`

Now that syslog is running, let's configure Docker to use syslog as the default logging driver. We'll do this by creating a file called `daemon.json`.

`mkdir /etc/docker
vi /etc/docker/daemon.json`

In the vi editor, enter the following, making sure to replace `<PRIVATE_IP>` with the private IP of your cloud server:

`{
  "log-driver": "syslog",
  "log-opts": {
    "syslog-address": "udp://<PRIVATE_IP>:514"
  }
}`

Next, save and quit. Then, start the Docker service.

`systemctl start docker`

The next step is to see if there are any logs coming in from Docker.

`tail /var/log/messages`

The output of this command tells us that there are. Now let's test our setup.

We're going to create two new containers using the `httpd` image. The first one will be called `syslog-logging` and will use `none` for the log driver. The second will be called `json-logging` and will use the JSON file for the log driver.

Let's create our first container.

`docker container run -d --name syslog-logging --log-driver none httpd`

Then run `docker ps` to make sure our container is up and running correctly.

Next, execute the `docker logs` command to see what logs we have.

`docker logs syslog-logging`

To confirm that we are logging to syslog, we can check the content of `/var/log/messages`. Verify that the `syslog-logging` container is sending its logs to syslog by running `tail` on the message log file.

`tail /var/log/messages`

The output shows us the logs that are being input to syslog.

Now let's create our second test container. This time, we'll specify the log driver as the JSON file.

`docker container run -d --name json-logging --log-driver json-file httpd`

Run the `docker ps` command again. This time, we should see two containers running.

Next, verify that the `json-logging` container is sending its logs to the JSON file. Execute the `docker logs` command on the `json-logging` container.

`docker logs json-logging`

This time, the logs do not appear in `/var/log/messages` because they are being sent to a JSON file instead.

# Log

```sh
Host '54.81.152.113' is not in the trusted hosts file.
(ssh-ed25519 fingerprint sha1!! d9:8f:ec:f6:1b:62:4c:d2:73:58:b8:17:c3:8c:97:4d:d3:8f:b8:bd)
Do you want to continue connecting? (y/n) y
cloud_user@54.81.152.113's password:
[cloud_user@ip-10-0-1-214 ~]$ sudo su -
[sudo] password for cloud_user:
[root@ip-10-0-1-214 ~]#

[root@ip-10-0-1-214 ~]# mkdir /etc/docker
mkdir: cannot create directory ‘/etc/docker’: File exists
[root@ip-10-0-1-214 ~]# vi /etc/docker/daemon.json
[root@ip-10-0-1-214 ~]# cat /etc/docker/daemon.json
{
  "log-driver": "syslog",
  "log-opts": {
    "syslog-address": "udp://10.0.1.214:514"
  }
}


[root@ip-10-0-1-214 ~]# systemctl start docker
[root@ip-10-0-1-214 ~]#
[root@ip-10-0-1-214 ~]#
[root@ip-10-0-1-214 ~]# tail /var/log/messages
Apr  4 17:29:35 ip-10-0-1-214 dockerd: time="2023-04-04T17:29:35.298526121-04:00" level=info msg="Default bridge (docker0) is assigned with an IP address 172.17.0.0/16. Daemon option --bip can be used to set a preferred IP address"
Apr  4 17:29:35 ip-10-0-1-214 firewalld[539]: WARNING: COMMAND_FAILED: '/usr/sbin/iptables -w10 -D FORWARD -i docker0 -o docker0 -j DROP' failed: iptables: Bad rule (does a matching rule exist in that chain?).
Apr  4 17:29:35 ip-10-0-1-214 dockerd: time="2023-04-04T17:29:35.408371637-04:00" level=info msg="Firewalld: interface docker0 already part of docker zone, returning"
Apr  4 17:29:35 ip-10-0-1-214 kernel: IPv6: ADDRCONF(NETDEV_UP): docker0: link is not ready
Apr  4 17:29:35 ip-10-0-1-214 dockerd: time="2023-04-04T17:29:35.505332992-04:00" level=info msg="Loading containers: done."
Apr  4 17:29:35 ip-10-0-1-214 dockerd: time="2023-04-04T17:29:35.560424009-04:00" level=info msg="Docker daemon" commit=219f21b graphdriver=overlay2 version=23.0.2
Apr  4 17:29:35 ip-10-0-1-214 dockerd: time="2023-04-04T17:29:35.560619412-04:00" level=info msg="Daemon has completed initialization"
Apr  4 17:29:35 ip-10-0-1-214 dockerd: time="2023-04-04T17:29:35.585810314-04:00" level=info msg="[core] [Server #7] Server created" module=grpc
Apr  4 17:29:35 ip-10-0-1-214 systemd: Started Docker Application Container Engine.
Apr  4 17:29:35 ip-10-0-1-214 dockerd: time="2023-04-04T17:29:35.599396552-04:00" level=info msg="API listen on /run/docker.sock"
[root@ip-10-0-1-214 ~]#

## We see messages from docker ^^^

[root@ip-10-0-1-214 ~]# docker container run -d --name syslog-logging --log-driver none httpd
Unable to find image 'httpd:latest' locally
latest: Pulling from library/httpd
f1f26f570256: Pull complete
a6b093ae1967: Pull complete
6b400bbb27df: Pull complete
d9833ead928a: Pull complete
ace056404ed3: Pull complete
Digest: sha256:f3e9eb9acace5bbc13c924293d2247a65bb59b8f062bcd98cd87ee4e18f86733
Status: Downloaded newer image for httpd:latest
7e585ab436a78d225258b69756da567bfad9250899d1c48cd3cb785ae12a53ff
[root@ip-10-0-1-214 ~]# docker ps
CONTAINER ID   IMAGE     COMMAND              CREATED          STATUS         PORTS     NAMES
7e585ab436a7   httpd     "httpd-foreground"   11 seconds ago   Up 9 seconds   80/tcp    syslog-logging
[root@ip-10-0-1-214 ~]# docker logs
"docker logs" requires exactly 1 argument.
See 'docker logs --help'.

Usage:  docker logs [OPTIONS] CONTAINER

Fetch the logs of a container
[root@ip-10-0-1-214 ~]# docker logs syslog-logging
Error response from daemon: configured logging driver does not support reading


[root@ip-10-0-1-214 ~]# tail /var/log/messages
Apr  4 17:30:48 ip-10-0-1-214 containerd: time="2023-04-04T17:30:48.542438574-04:00" level=info msg="loading plugin \"io.containerd.internal.v1.shutdown\"..." runtime=io.containerd.runc.v2 type=io.containerd.internal.v1
Apr  4 17:30:48 ip-10-0-1-214 containerd: time="2023-04-04T17:30:48.542459598-04:00" level=info msg="loading plugin \"io.containerd.ttrpc.v1.task\"..." runtime=io.containerd.runc.v2 type=io.containerd.ttrpc.v1
Apr  4 17:30:48 ip-10-0-1-214 containerd: time="2023-04-04T17:30:48.543556712-04:00" level=info msg="starting signal loop" namespace=moby path=/run/containerd/io.containerd.runtime.v2.task/moby/7e585ab436a78d225258b69756da567bfad9250899d1c48cd3cb785ae12a53ff pid=2630 runtime=io.containerd.runc.v2
Apr  4 17:30:48 ip-10-0-1-214 kernel: IPv6: ADDRCONF(NETDEV_UP): eth0: link is not ready
Apr  4 17:30:48 ip-10-0-1-214 kernel: IPv6: ADDRCONF(NETDEV_CHANGE): eth0: link becomes ready
Apr  4 17:30:48 ip-10-0-1-214 kernel: IPv6: ADDRCONF(NETDEV_CHANGE): veth41f1f22: link becomes ready
Apr  4 17:30:48 ip-10-0-1-214 kernel: docker0: port 1(veth41f1f22) entered blocking state
Apr  4 17:30:48 ip-10-0-1-214 kernel: docker0: port 1(veth41f1f22) entered forwarding state
Apr  4 17:30:48 ip-10-0-1-214 kernel: IPv6: ADDRCONF(NETDEV_CHANGE): docker0: link becomes ready
Apr  4 17:31:14 ip-10-0-1-214 dockerd: time="2023-04-04T17:31:14.672033389-04:00" level=error msg="Handler for GET /v1.42/containers/7e585ab436a78d225258b69756da567bfad9250899d1c48cd3cb785ae12a53ff/logs returned error: configured logging driver does not support reading"

# Note that the error from the container went to syslog ^^^

## Run second container

root@ip-10-0-1-214 ~]# docker container run -d --name json-logging --log-driver json-file httpd
adba6933a5b0a7a5b6ce322f3038415e9f6be0848585e9b56bbadef028c6d063
[root@ip-10-0-1-214 ~]#
[root@ip-10-0-1-214 ~]# docker ps
CONTAINER ID   IMAGE     COMMAND              CREATED          STATUS          PORTS     NAMES
adba6933a5b0   httpd     "httpd-foreground"   11 seconds ago   Up 11 seconds   80/tcp    json-logging
7e585ab436a7   httpd     "httpd-foreground"   9 minutes ago    Up 9 minutes    80/tcp    syslog-logging
[root@ip-10-0-1-214 ~]#
[root@ip-10-0-1-214 ~]#
[root@ip-10-0-1-214 ~]# docker logs json-logging
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.3. Set the 'ServerName' directive globally to suppress this message
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.3. Set the 'ServerName' directive globally to suppress this message
[Tue Apr 04 21:39:46.713809 2023] [mpm_event:notice] [pid 1:tid 139715794165056] AH00489: Apache/2.4.56 (Unix) configured -- resuming normal operations
[Tue Apr 04 21:39:46.714034 2023] [core:notice] [pid 1:tid 139715794165056] AH00094: Command line: 'httpd -D FOREGROUND'
[root@ip-10-0-1-214 ~]#
[root@ip-10-0-1-214 ~]#
[root@ip-10-0-1-214 ~]# tail /var/log/messages
Apr  4 17:39:46 ip-10-0-1-214 kernel: docker0: port 2(vethfe2ebe4) entered disabled state
Apr  4 17:39:46 ip-10-0-1-214 containerd: time="2023-04-04T17:39:46.351247804-04:00" level=info msg="loading plugin \"io.containerd.event.v1.publisher\"..." runtime=io.containerd.runc.v2 type=io.containerd.event.v1
Apr  4 17:39:46 ip-10-0-1-214 containerd: time="2023-04-04T17:39:46.351328133-04:00" level=info msg="loading plugin \"io.containerd.internal.v1.shutdown\"..." runtime=io.containerd.runc.v2 type=io.containerd.internal.v1
Apr  4 17:39:46 ip-10-0-1-214 containerd: time="2023-04-04T17:39:46.351343873-04:00" level=info msg="loading plugin \"io.containerd.ttrpc.v1.task\"..." runtime=io.containerd.runc.v2 type=io.containerd.ttrpc.v1
Apr  4 17:39:46 ip-10-0-1-214 containerd: time="2023-04-04T17:39:46.352243084-04:00" level=info msg="starting signal loop" namespace=moby path=/run/containerd/io.containerd.runtime.v2.task/moby/adba6933a5b0a7a5b6ce322f3038415e9f6be0848585e9b56bbadef028c6d063 pid=2899 runtime=io.containerd.runc.v2
Apr  4 17:39:46 ip-10-0-1-214 kernel: IPv6: ADDRCONF(NETDEV_UP): eth0: link is not ready
Apr  4 17:39:46 ip-10-0-1-214 kernel: IPv6: ADDRCONF(NETDEV_CHANGE): eth0: link becomes ready
Apr  4 17:39:46 ip-10-0-1-214 kernel: IPv6: ADDRCONF(NETDEV_CHANGE): vethfe2ebe4: link becomes ready
Apr  4 17:39:46 ip-10-0-1-214 kernel: docker0: port 2(vethfe2ebe4) entered blocking state
Apr  4 17:39:46 ip-10-0-1-214 kernel: docker0: port 2(vethfe2ebe4) entered forwarding state



```