# Hands on Docker

## 1. Docker installation

To install docker you can execute the script install_docker.sh in the config folder.

## 2. Download our first image

- To download our first image: 
`docker pull <docker_image>`

## 3. Run our first containers

`docker run -p 80:80 nginx`

## 4. Create a volume

`docker run -p 80:80 -v /opt/container_data:/usr/share/nginx/html` 

## 5. Create our docker image using Dockerfile

Create a docker file for the the golang app in the src/app folder 





