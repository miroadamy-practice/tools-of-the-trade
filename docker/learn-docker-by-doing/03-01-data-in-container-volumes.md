# Storing Container Data In Docker Volumes

## ABOUT THIS LAB

Storing data within a container image is one option for automating a container with data, but it requires a copy of the data to be in each container you run. For static files, this can be a waste of resources. Each file might not amount to more than a few megabytes, but once the containers are scaled up to handle a production load, that few megabytes can turn into gigabytes of waste. Instead, you can store one copy of the static files in a Docker volume for easy sharing between containers. In this lab, you will learn how Docker volumes interact with containers. You will do this by creating new volumes and attaching them to containers. You'll then clean up space left by anonymous volumes created automatically by the containers. Finally, you'll learn about backup strategies for your volumes.

