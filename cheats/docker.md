# docker

```bash
# Build image named `kubia` from Dockerfile in current directory
docker build -t kubia .

# start container kubia-container in background and forward
# port 8080 from container to port 8080 on local machine
docker run --name kubia-container -p 8080:8080 -d

# inspect locally running container named kubia-container
dk inspect kubia-container

# stop container with id e5d63f08a2e2
docker stop e5d63f08a2e2

# delete container with id 91f1f43ba8c1cd2b718c12ee07ee3f7d5508070927dded4dd6ac0393edae75ec
docker rm 91f1f43ba8c1cd2b718c12ee07ee3f7d5508070927dded4dd6ac0393edae75ec

# show all running containers
docker ps

# show all containers (including stopped ones)
docker ps -a

# show all docker images
docker images

# add tag murchu/kubia to image named kubia
docker tag kubia murchu/kubia

# login to docker.io
docker login

# login to quay.io
docker login quay.io

# push image tagged murchu/kubia to docker.io
docker push murchu/kubia

# push image tagged murchu/kubia to quay.io
docker push murchu/kubia quay.io
```