# Run Jenkins Image in Docker: Java Maven App
[Reference 1:Jenkins website--Build a Java app with Maven](https://www.jenkins.io/doc/tutorials/build-a-java-app-with-maven/)
Set up and buid a docker image for jenkins on macOS <br/>
Create a bridge network in Docker 
```
docker network create jenkins
```
Download and run the docker:dind Docker image
``` 
docker run \
  --name jenkins-docker \
  --rm \
  --detach \
  --privileged \
  --network jenkins \
  --network-alias docker \
  --env DOCKER_TLS_CERTDIR=/certs \
  --volume jenkins-docker-certs:/certs/client \
  --volume jenkins-data:/var/jenkins_home \
  --publish 2376:2376 \
  --publish 3000:3000 \
  docker:dind \
  --storage-driver overlay2
  ```
  Build a new docker image from this Dockerfile and assign the image a meaningful name, e.g. "myjenkins-blueocean:2.346.2-1":
  ```
  docker build -t myjenkins-blueocean:2.346.2-1 .
  ```
  Run your own myjenkins-blueocean:2.346.2-1 image as a container in Docker
  ```
  docker run \
  --name jenkins-blueocean \
  --detach \
  --network jenkins \
  --env DOCKER_HOST=tcp://docker:2376 \
  --env DOCKER_CERT_PATH=/certs/client \
  --env DOCKER_TLS_VERIFY=1 \
  --publish 8080:8080 \
  --publish 50000:50000 \
  --volume jenkins-data:/var/jenkins_home \
  --volume jenkins-docker-certs:/certs/client:ro \
  --volume "$HOME":/home \
  --restart=on-failure \
  --env JAVA_OPTS="-Dhudson.plugins.git.GitSCM.ALLOW_LOCAL_CHECKOUT=true" \
  myjenkins-blueocean:2.346.2-1 
```
Now your jenkins server is running on jenkins-blueocean docker container
```
docker container exec -it jenkins-blueocean bash
```
Yo can access Jenkins on http://localhost:8080<br/>
To access verification password on container bash run<br/>
```
cat /var/jenkins_home/secrets/initialAdminPassword
```
to exit from container bash
```
exit
```
To see jenkins running container logs
```
docker logs jenkins-blueocean
```


