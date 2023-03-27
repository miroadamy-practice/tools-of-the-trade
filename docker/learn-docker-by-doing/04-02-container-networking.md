# Docker Container Networking

## Introduction

Each container should serve a single purpose, such as running one application like a web server. Containers can be powerful by themselves, but when connected together, they are far more useful.

For example, a web server container can be connected to a database container to provide application storage. Docker provides multiple options for networking containers.

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

