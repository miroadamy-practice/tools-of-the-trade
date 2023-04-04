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

