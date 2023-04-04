# Updating Containers with Watchtower

For the last six months, the Acme Anvil Corporation has been migrating some of their bare metal infrastructure to Docker containers. After the initial implementation, the team has decided that they need a way to manage container updates.

After doing some research, the team has discovered Watchtower. Watchtower is a container that updates all running containers when changes are made to the image that it is running.

You have been tasked with creating a proof of concept for the team. You will need to create a Dockerfile that will be used to create a Docker image. The image will be pushed to Docker Hub. Next, you will create a container using this image. Once the image is created, you will deploy the Watchtower container. After Watchtower is deployed, you will update the Dockerfile, rebuild the image, and push the changes to Docker Hub. Watchtower checks for changes every 30 seconds. Once it detects the changes, Watchtower will update the running container.

For more information on Watchtower you can go here.

LEARNING OBJECTIVES

* Create the Dockerfile
* Build the Docker Image
* Push the Image to Docker Hub
* Create a Demo Container
* Create the Watchtower Container
* Update the Docker Image


