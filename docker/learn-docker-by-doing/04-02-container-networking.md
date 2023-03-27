# Docker Container Networking

## Introduction

Each container should serve a single purpose, such as running one application like a web server. Containers can be powerful by themselves, but when connected together, they are far more useful.

For example, a web server container can be connected to a database container to provide application storage. Docker provides multiple options for networking containers. (5 OOTB, more with plugins)

In this lab, you'll explore a few of the common types of networks that Docker supports, and learn how containers within those networks interact.

## Solution

Log in to the server using the credentials provided:

`ssh cloud_user@<PUBLIC_IP_ADDRESS>`

### Explore the Default Network

1. List the default networks:
    
    `docker network ls`
    
2. Run an `httpd` container named `web1`, without specifying a network, and see which network it uses:
    
    `docker run -d --name web1 httpd:2.4`
    
    `docker inspect web1`
    
3. Take note of the `IPAddress`.
4. Run a container using the `busybox` image, and see if you can connect to the `web1` server:
    
    `docker run --rm -it busybox`
    
5. Check the container's networking, and verify it is in the same IP range as `web1`:
    
    `ip addr`
    
6. Ping the `web1` container using the IP address:
    
    `ping <WEB1_IP_ADDRESS>`
    
7. Attempt to ping the `web1` container by name:
    
    `ping web1`
    
8. Attempt to access `web1` using `wget`:
    
    `wget <WEB1_IP_ADDRESS>`
    
9. Exit the container:
    
    `exit`
    

### Explore Bridge Networks

1. Create a new bridge network named `test_application`:
    
    `docker network create test_application`
    
2. Run an `httpd` container named `web2`, in the `test_application` network:
    
    `docker run -d --name web2 --network test_application httpd:2.4`
    
3. Check the status of the container:
    
    `docker ps -a`
    
4. Verify that `web2` was added to the `test_application` network:
    
    `docker inspect web2`
    
5. Run a container using the `busybox` image, and see if you can connect to the `web2` server, within the `test_application` network:
    
    `docker run --rm -it --network test_application busybox`
    
6. Check the container's networking, and verify it is in the same IP range as `web2`:
    
    `ip addr`
    
7. Ping the `web2` container using the IP address:
    
    `ping <WEB2_IP_ADDRESS>`
    
8. Attempt to ping the `web2` container by name:
    
    `ping web2`
    
9. Using `wget`, attempt to access `web2` with the hostname:
    
    `wget web2`
    
10. Attempt to ping `web1`:
    
    `ping <WEB1_IP_ADDRESS>`
    
11. Attempt to access `web1` using `wget`:
    
    `wget <WEB1_IP_ADDRESS>`
    
12. Exit the container:
    
    `exit`
    

### Explore the Host Network

1. Run an `httpd` container named `web3` on the `host` network:
    
    `docker run -d --name web3 --network host httpd:2.4`
    
2. Check the status of the container:
    
    `docker ps -a`
    
3. Attempt to connect to web3 directly from the server:
    
    `wget localhost`
    
4. Stop `web3`:
    
    `docker stop web3`
    
5. Attempt to connect to web3 directly from the server again:
    
    `wget localhost`
    
6. Start `web3`:
    
    `docker start web3`
    
7. Run a container using the `busybox` image, and see if you can connect to the `web3` server:
    
    `docker run --rm -it --network host busybox`
    
    `ping web3`
    
8. Using `wget`, attempt to access `localhost` within the `busybox` image:
    
    `wget localhost`
    
9. Attempt to ping `web2`:
    
    `ping <WEB2_IP_ADDRESS>`
    
10. Attempt to ping `web1`:
    
    `ping <WEB1_IP_ADDRESS>`


# LOG

```sh
/ $ ssh cloud_user@18.209.7.205

Host '18.209.7.205' is not in the trusted hosts file.
(ssh-ed25519 fingerprint sha1!! 1b:38:d1:1b:35:e8:3a:f3:7f:df:f3:f1:02:cb:67:aa:46:c4:d2:2b)
Do you want to continue connecting? (y/n) y
cloud_user@18.209.7.205's password:
[cloud_user@ip-10-0-1-144 ~]$ docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
c6411746a175   bridge    bridge    local
d705f209fbe4   host      host      local
b4796dee94e6   none      null      local
[cloud_user@ip-10-0-1-144 ~]$ docker run -d --name web1 httpd:2.4
9ecea3cca63f21a4e4b16883fcd1817164c23e46f106498f2dcc624d2a66c629
[cloud_user@ip-10-0-1-144 ~]$ docker ps -a
CONTAINER ID   IMAGE       COMMAND              CREATED          STATUS          PORTS     NAMES
9ecea3cca63f   httpd:2.4   "httpd-foreground"   11 seconds ago   Up 10 seconds   80/tcp    web1
[cloud_user@ip-10-0-1-144 ~]$ docker inspect web1
[
    {
        "Id": "9ecea3cca63f21a4e4b16883fcd1817164c23e46f106498f2dcc624d2a66c629",
        "Created": "2023-03-27T10:27:56.992892764Z",
        "Path": "httpd-foreground",
        "Args": [],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 2869,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2023-03-27T10:27:57.517795371Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:192d41583429c96af40eb3af02d4baaf6398c178e832c114abe030ff986ee826",
        "ResolvConfPath": "/var/lib/docker/containers/9ecea3cca63f21a4e4b16883fcd1817164c23e46f106498f2dcc624d2a66c629/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/9ecea3cca63f21a4e4b16883fcd1817164c23e46f106498f2dcc624d2a66c629/hostname",
        "HostsPath": "/var/lib/docker/containers/9ecea3cca63f21a4e4b16883fcd1817164c23e46f106498f2dcc624d2a66c629/hosts",
        "LogPath": "/var/lib/docker/containers/9ecea3cca63f21a4e4b16883fcd1817164c23e46f106498f2dcc624d2a66c629/9ecea3cca63f21a4e4b16883fcd1817164c23e46f106498f2dcc624d2a66c629-json.log",
        "Name": "/web1",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "ConsoleSize": [
                41,
                158
            ],
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "host",
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": [],
            "BlkioDeviceWriteBps": [],
            "BlkioDeviceReadIOps": [],
            "BlkioDeviceWriteIOps": [],
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/2216b6f865056f69356121fcd40abc6f0df1da2090005454db23069210af9047-init/diff:/var/lib/docker/overlay2/75a711531b28c7b6bc3244e37cd8eef170be9c0dee243ed28d16667a397bbc64/diff:/var/lib/docker/overlay2/84f1f469ff5a86a287fe8b3d17dca1c8179565272ae9e37d79b25db30d597f07/diff:/var/lib/docker/overlay2/e36e05c7319ebc43f7f3a9bb5ad41731278bf35f30c3711e6d79fedba55bee1d/diff:/var/lib/docker/overlay2/bee7c9f1816ffc770bdac8948bb23a7f574b00bfc00c344577f48a8f787b7552/diff:/var/lib/docker/overlay2/10f2ea6f4d8e578dc51a57c86b42c476df00662bd9b37ca8ddb45fbd62896024/diff",
                "MergedDir": "/var/lib/docker/overlay2/2216b6f865056f69356121fcd40abc6f0df1da2090005454db23069210af9047/merged",
                "UpperDir": "/var/lib/docker/overlay2/2216b6f865056f69356121fcd40abc6f0df1da2090005454db23069210af9047/diff",
                "WorkDir": "/var/lib/docker/overlay2/2216b6f865056f69356121fcd40abc6f0df1da2090005454db23069210af9047/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "9ecea3cca63f",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "80/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/apache2/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "HTTPD_PREFIX=/usr/local/apache2",
                "HTTPD_VERSION=2.4.56",
                "HTTPD_SHA256=d8d45f1398ba84edd05bb33ca7593ac2989b17cb9c7a0cafe5442d41afdb2d7c",
                "HTTPD_PATCHES="
            ],
            "Cmd": [
                "httpd-foreground"
            ],
            "Image": "httpd:2.4",
            "Volumes": null,
            "WorkingDir": "/usr/local/apache2",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {},
            "StopSignal": "SIGWINCH"
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "ee2e0b7ed0bb7b2f6de3802175a08a5e9c4d9a40a6e2641f639dd6545da0f8ec",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {
                "80/tcp": null
            },
            "SandboxKey": "/var/run/docker/netns/ee2e0b7ed0bb",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "82905b95bb1c207c4e07b30b25394dc68c50b27e0166e9c200a9a712a21505c2",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "c6411746a175f05296fa26840c2a9974070674919326e40a73bf8c24d2c707d9",
                    "EndpointID": "82905b95bb1c207c4e07b30b25394dc68c50b27e0166e9c200a9a712a21505c2",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]

```

Note  "IPAddress": "172.17.0.2",

3 of 5 are created on creation


```sh

cloud_user@ip-10-0-1-144 ~]$ docker run --rm -it busybox

/ # ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
6: eth0@if7: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 1500 qdisc noqueue
    link/ether 02:42:ac:11:00:03 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.3/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever


/ # ping 172.17.0.2
PING 172.17.0.2 (172.17.0.2): 56 data bytes
64 bytes from 172.17.0.2: seq=0 ttl=64 time=0.135 ms
64 bytes from 172.17.0.2: seq=1 ttl=64 time=0.099 ms
^C
--- 172.17.0.2 ping statistics ---
2 packets transmitted, 2 packets received, 0% packet loss
round-trip min/avg/max = 0.099/0.117/0.135 ms
/ # ping web1
ping: bad address 'web1'
/ # wget 172.17.0.2
Connecting to 172.17.0.2 (172.17.0.2:80)
saving to 'index.html'
index.html           100% |**************************************************************************************************************|    45  0:00:00 ETA
'index.html' saved


```

In default bridge network, only by IP works, not find by name

Access to port works even if port is not published => we are ion the same network

Create new bridge network

```sh
cloud_user@ip-10-0-1-144 ~]$ docker network create test_application
ed3295bab46e19983a028c9eb0bbc62dee838bce627001bad45e910114baa2f1
[cloud_user@ip-10-0-1-144 ~]$ docker network ls
NETWORK ID     NAME               DRIVER    SCOPE
c6411746a175   bridge             bridge    local
d705f209fbe4   host               host      local
b4796dee94e6   none               null      local
ed3295bab46e   test_application   bridge    local
[cloud_user@ip-10-0-1-144 ~]$ docker run -d --name web2 --network test_application httpd:2.4
8c1d1650620126aed9345960ec147db121a0cca942b700eb71f258da54e8ffd7
[cloud_user@ip-10-0-1-144 ~]$ docker ps -a
CONTAINER ID   IMAGE       COMMAND              CREATED          STATUS          PORTS     NAMES
8c1d16506201   httpd:2.4   "httpd-foreground"   8 seconds ago    Up 7 seconds    80/tcp    web2
9ecea3cca63f   httpd:2.4   "httpd-foreground"   16 minutes ago   Up 16 minutes   80/tcp    web1
[cloud_user@ip-10-0-1-144 ~]$ docker inspect web2
[
    {
        "Id": "8c1d1650620126aed9345960ec147db121a0cca942b700eb71f258da54e8ffd7",
        "Created": "2023-03-27T10:43:57.598270549Z",
        "Path": "httpd-foreground",
        "Args": [],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 3382,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2023-03-27T10:43:58.051942144Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:192d41583429c96af40eb3af02d4baaf6398c178e832c114abe030ff986ee826",
        "ResolvConfPath": "/var/lib/docker/containers/8c1d1650620126aed9345960ec147db121a0cca942b700eb71f258da54e8ffd7/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/8c1d1650620126aed9345960ec147db121a0cca942b700eb71f258da54e8ffd7/hostname",
        "HostsPath": "/var/lib/docker/containers/8c1d1650620126aed9345960ec147db121a0cca942b700eb71f258da54e8ffd7/hosts",
        "LogPath": "/var/lib/docker/containers/8c1d1650620126aed9345960ec147db121a0cca942b700eb71f258da54e8ffd7/8c1d1650620126aed9345960ec147db121a0cca942b700eb71f258da54e8ffd7-json.log",
        "Name": "/web2",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "test_application",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "ConsoleSize": [
                41,
                158
            ],
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "host",
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": [],
            "BlkioDeviceWriteBps": [],
            "BlkioDeviceReadIOps": [],
            "BlkioDeviceWriteIOps": [],
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/c09c123fc127f6144b4bdbcf6ae1dac346de54aa15878291e007fa5e26a59314-init/diff:/var/lib/docker/overlay2/75a711531b28c7b6bc3244e37cd8eef170be9c0dee243ed28d16667a397bbc64/diff:/var/lib/docker/overlay2/84f1f469ff5a86a287fe8b3d17dca1c8179565272ae9e37d79b25db30d597f07/diff:/var/lib/docker/overlay2/e36e05c7319ebc43f7f3a9bb5ad41731278bf35f30c3711e6d79fedba55bee1d/diff:/var/lib/docker/overlay2/bee7c9f1816ffc770bdac8948bb23a7f574b00bfc00c344577f48a8f787b7552/diff:/var/lib/docker/overlay2/10f2ea6f4d8e578dc51a57c86b42c476df00662bd9b37ca8ddb45fbd62896024/diff",
                "MergedDir": "/var/lib/docker/overlay2/c09c123fc127f6144b4bdbcf6ae1dac346de54aa15878291e007fa5e26a59314/merged",
                "UpperDir": "/var/lib/docker/overlay2/c09c123fc127f6144b4bdbcf6ae1dac346de54aa15878291e007fa5e26a59314/diff",
                "WorkDir": "/var/lib/docker/overlay2/c09c123fc127f6144b4bdbcf6ae1dac346de54aa15878291e007fa5e26a59314/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "8c1d16506201",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "80/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/apache2/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "HTTPD_PREFIX=/usr/local/apache2",
                "HTTPD_VERSION=2.4.56",
                "HTTPD_SHA256=d8d45f1398ba84edd05bb33ca7593ac2989b17cb9c7a0cafe5442d41afdb2d7c",
                "HTTPD_PATCHES="
            ],
            "Cmd": [
                "httpd-foreground"
            ],
            "Image": "httpd:2.4",
            "Volumes": null,
            "WorkingDir": "/usr/local/apache2",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {},
            "StopSignal": "SIGWINCH"
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "b5e03cf79158c84622ac6712eb00227e0d7420ca1b168d4ba03b4a30e4d479cc",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {
                "80/tcp": null
            },
            "SandboxKey": "/var/run/docker/netns/b5e03cf79158",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "",
            "Gateway": "",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "",
            "IPPrefixLen": 0,
            "IPv6Gateway": "",
            "MacAddress": "",
            "Networks": {
                "test_application": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": [
                        "8c1d16506201"
                    ],
                    "NetworkID": "ed3295bab46e19983a028c9eb0bbc62dee838bce627001bad45e910114baa2f1",
                    "EndpointID": "a0d2bfa42b7e448c553e04e5600569afbe390bb17422c1fc4f519c77cfb26d30",
                    "Gateway": "172.18.0.1",
                    "IPAddress": "172.18.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:12:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]

[cloud_user@ip-10-0-1-144 ~]$ docker run --rm -it --network test_application busybox
/ # ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
11: eth0@if12: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 1500 qdisc noqueue
    link/ether 02:42:ac:12:00:03 brd ff:ff:ff:ff:ff:ff
    inet 172.18.0.3/16 brd 172.18.255.255 scope global eth0
       valid_lft forever preferred_lft forever
/ #

/ # ping 172.18.0.2
PING 172.18.0.2 (172.18.0.2): 56 data bytes
64 bytes from 172.18.0.2: seq=0 ttl=64 time=0.143 ms
64 bytes from 172.18.0.2: seq=1 ttl=64 time=0.098 ms
^C
--- 172.18.0.2 ping statistics ---
2 packets transmitted, 2 packets received, 0% packet loss
round-trip min/avg/max = 0.098/0.120/0.143 ms
/ # ping 172.17.0.2
PING 172.17.0.2 (172.17.0.2): 56 data bytes
^C
--- 172.17.0.2 ping statistics ---
4 packets transmitted, 0 packets received, 100% packet loss
/ # ping web2
PING web2 (172.18.0.2): 56 data bytes
64 bytes from 172.18.0.2: seq=0 ttl=64 time=0.091 ms
64 bytes from 172.18.0.2: seq=1 ttl=64 time=0.102 ms
64 bytes from 172.18.0.2: seq=2 ttl=64 time=0.108 ms
^C
--- web2 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.091/0.100/0.108 ms
/ # wget web2
Connecting to web2 (172.18.0.2:80)
saving to 'index.html'
index.html           100% |**************************************************************************************************************|    45  0:00:00 ETA
'index.html' saved
```

In named bridge network, the names work

## Host network

```sh
[cloud_user@ip-10-0-1-144 ~]$ docker ps -a
CONTAINER ID   IMAGE       COMMAND              CREATED          STATUS          PORTS     NAMES
8c1d16506201   httpd:2.4   "httpd-foreground"   11 minutes ago   Up 11 minutes   80/tcp    web2
9ecea3cca63f   httpd:2.4   "httpd-foreground"   27 minutes ago   Up 27 minutes   80/tcp    web1

[cloud_user@ip-10-0-1-144 ~]$ docker run -d --name web3 --network host httpd:2.4
21f0aaa3debcf413fde1f338df2aed9d90ec4ff8ef88e64e95b5a3f6b84b3ad9

[cloud_user@ip-10-0-1-144 ~]$ wget localhost
--2023-03-27 06:55:54--  http://localhost/
Resolving localhost (localhost)... ::1, 127.0.0.1
Connecting to localhost (localhost)|::1|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 45 [text/html]
Saving to: ‘index.html’

100%[====================================================================================================================>] 45          --.-K/s   in 0s

2023-03-27 06:55:54 (7.06 MB/s) - ‘index.html’ saved [45/45]

[cloud_user@ip-10-0-1-144 ~]$ docker stop web3
web3

[cloud_user@ip-10-0-1-144 ~]$ docker ps -a
CONTAINER ID   IMAGE       COMMAND              CREATED          STATUS                     PORTS     NAMES
21f0aaa3debc   httpd:2.4   "httpd-foreground"   29 seconds ago   Exited (0) 3 seconds ago             web3
8c1d16506201   httpd:2.4   "httpd-foreground"   12 minutes ago   Up 12 minutes              80/tcp    web2
9ecea3cca63f   httpd:2.4   "httpd-foreground"   28 minutes ago   Up 28 minutes              80/tcp    web1

[cloud_user@ip-10-0-1-144 ~]$ wget localhost
--2023-03-27 06:56:12--  http://localhost/
Resolving localhost (localhost)... ::1, 127.0.0.1
Connecting to localhost (localhost)|::1|:80... failed: Connection refused.
Connecting to localhost (localhost)|127.0.0.1|:80... failed: Connection refused.

[cloud_user@ip-10-0-1-144 ~]$ docker start web3
web3

[cloud_user@ip-10-0-1-144 ~]$ docker run --rm -it --network host busybox
/ # ping web3
ping: bad address 'web3'
/ # wget localhost
Connecting to localhost (127.0.0.1:80)
saving to 'index.html'
index.html           100% |**************************************************************************************************************|    45  0:00:00 ETA
'index.html' saved
/ # wget 172.17.0.2
Connecting to 172.17.0.2 (172.17.0.2:80)
wget: can't open 'index.html': File exists
/ # wget 172.18.0.2
Connecting to 172.18.0.2 (172.18.0.2:80)
wget: can't open 'index.html': File exists
/ # ping 172.17.0.2
PING 172.17.0.2 (172.17.0.2): 56 data bytes
64 bytes from 172.17.0.2: seq=0 ttl=64 time=0.090 ms
64 bytes from 172.17.0.2: seq=1 ttl=64 time=0.090 ms
^C
--- 172.17.0.2 ping statistics ---
2 packets transmitted, 2 packets received, 0% packet loss
round-trip min/avg/max = 0.090/0.090/0.090 ms
/ # ping 172.17.0.3
PING 172.17.0.3 (172.17.0.3): 56 data bytes
^C
--- 172.17.0.3 ping statistics ---
5 packets transmitted, 0 packets received, 100% packet loss
/ # ping 172.18.0.2
PING 172.18.0.2 (172.18.0.2): 56 data bytes
64 bytes from 172.18.0.2: seq=0 ttl=64 time=0.095 ms
64 bytes from 172.18.0.2: seq=1 ttl=64 time=0.095 ms
^C
--- 172.18.0.2 ping statistics ---
2 packets transmitted, 2 packets received, 0% packet loss
round-trip min/avg/max = 0.095/0.095/0.095 ms
```

We can connect from host to any IP

The name does not work

