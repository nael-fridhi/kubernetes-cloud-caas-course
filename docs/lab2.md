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

- Create a docker file for the the golang app in the src/app folder.

## 6. Inspect the layers of containers

<pre><code>
docker pull debian
docker history debian
docker inspect debian
docker inspect debian | grep Cmd --after-context=10
docker inspect debian | grep User --before-context=5
docker inspect debian | grep Id
</pre></code>


## 7. Set the default logging driver to syslog 


<pre><code>
sudo vi /etc/docker/daemon.json
# Add this configuration
{
  "log-driver": "syslog"
}
sudo systemctl restart docker
docker info | grep Logging

</pre></code>

## 8. Handcrafting a container image

- Create a dockerfile for the containerhub application using alpine base image
- Enter to the container and change whatever you want in the containerhub files
- Create a new version of the image with the modification you have done using `docker commit`

## 9. Storing Container Data in Docker Volumes

- Run two postgres:12.1 containers in detached mode.

- Inspect the postgres containers to learn about the attached volumes. </br>
`docker inspect <container_name> -f '{{ json .Mounts }}' | python -m json.tool` </br>

- Run another detached postgres container using the --rm flag.

- List the Docker volumes, then shut down the postgres containers to see what effect that has on the volumes.

- Create a Docker volume for the website containerhub code 

- Copy the website code into the volume from the host. You will need to use root permissions.

- use the website volume with container (you have to choose between nginx httpd) to deploy a the website

- Inspect the website Docker volume to find its location on disk.

- Back up the Docker volume from the host by using tar to create a compressed archive. </br>
`tar czf /tmp/website_$(date +%Y-%m-%d-%H%M).tgz -C /var/lib/docker/volumes/website/_data .` </br>

- Restore a backup.</br>
`tar xf <BACKUP_FILE_NAME>.tgz .`

## 10. Building smaller images with multi-stage builds

- Inspect the current Dockerfile for the NotesApp image.
- Build the image as notesapp and tag it as version default.
- Inspect the default image version, to see how many layers it has, and how large the image is.
- Move the install steps in the Dockerfile to a separate stage.
- Copy the finished installs into the final image.
- Build the image as notesapp and tag it as version multistage.
- Inspect the new multistage image version to see how many layers it has and how large the image is.


# 11. Docker monitoring using prometheus and grafana

Using the folder docker/prometheus try to run a monitoring platform using docker-compose 


# 12. Adding Metadata and Labels

- Come back to the dockerfile you write for the golang application:
  - Add three arguments:
    - BUILD_VERSION
    - BUILD_DATE
    - APPLICATION_NAME
  - Add three labels:
    - org.label-schema.build-date (will be set using the BUILD_DATE argument)
    - org.label-schema.application (will be set using the APPLICATION_NAME argument)
    - org.label-schema.version (will be set using the BUILD_VERSION argument)

- Build the docker image : 
``` 
docker build -t <DOCKER_USERNAME>/aidodev-docker-demo-app --build-arg BUILD_DATE=$(date -u +'%Y-%m-%dT%H:%M:%SZ') \
--build-arg APPLICATION_NAME=golang-sample-demo --build-arg BUILD_VERSION=v1.0  -f Dockerfile .
``` 

- Push the docker image to the dockerhub
- Change anything you want in the app and build the image v1.1

# 