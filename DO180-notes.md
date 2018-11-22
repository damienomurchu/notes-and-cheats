# DO180 Notes

- can create an image from a running container or a Dockerfile
- kubernetes resources
  - pods, scervices, replication controllers, persistent volumes, pv claims
- openshift resources
  - deployment configs
  - build configs
  - routes
- Routess: HTTP and TLS accesses to a route are forwarded to service addresses inside the Kubernetes SDN.
- Kubernetes vs OpenShift
  - Kubernetes manages load balancing, high availability and persistent storage for containerized applications.
  - OpenShift adds to Kubernetes multitenancy, security, ease of use, and continuous integration and continuous development features.
  - OpenShift routes are key to exposing containerized applications to external users in a manageable way


# docker

```bash
# search dockerhub for mysql images
docker search mysql

# run in background
docker run -d mysql:5.5

# name the container rather than auto-assign one
docker run --name mysql-container mysql:5.5

# run container & open up bash terminal in it
docker run --name mysql-container -it mysql:5.5 /bin/bash

# run with name, in background, and pass vars
docker run --name mysql-custom \
-e MYSQL_USER=redhat -e MYSQL_PASSWORD=r3dh4t \
-d mysql:5.5

# running containers
docker ps

# all containers
docker ps -a

# red hat container catalog: https://access.redhat.com/containers/

docker stop mysql-basic
docker rm mysql-basic
docker rmi rhscl/mysql-57-rhel7:5.7-3.14
docker run -d --name httpd-basic -p 8080:80 httpd:2.4
```

# MySQL
```bash
# log in as root to db
mysql -uroot -p
mysql> show databases;
mysql> use items;
mysql> CREATE TABLE Projects (id int(11) NOT NULL, name varchar(255) DEFAULT NULL, code varchar(255) DEFAULT NULL, PRIMARY KEY (id));
mysql> show tables;
mysql> insert into Projects (id, name, code) values (1,'DevOps','DO180');
mysql> select * from Projects;
mysql> exit
```

# Chapter 2 Summary

- Red Hat OpenShift Container Platform can be installed from RPM packages, from the client as a container, and in a dedicated virtual machine from Red Hat Container Development Kit (CDK).
  - The RPM installation usually configures a cluster of multiple Linux nodes for production, QA, and testing environments.
  - The containerized and CDK installations can be performed on a developer workstation, and supports Linux, MacOS, and Windows operating systems.
- The minishift command from the CDK unpacks the virtual machine images, installs client tools, and starts the OpenShift cluster inside the virtual machine.
- The oc cluster up command from the OpenShift client starts the local all-in-one cluster inside a container. It provides many command-line options to adapt to offline environments.
- Before starting the local all-in-one cluster, the Docker daemon must be configured to allow accessing the local insecure registry.
- The Docker Hub website provides a web interface to search for container images developed by the community and corporations. The Docker client can also search for images in Docker Hub.
- The docker run command creates and starts a container from an image that can be pulled by the local Docker daemon.
- Container images might require environment variables that are set using the -e option from the docker run command.
- Red Hat Container Catalog assists in searching, exploring, and analyzing container images from Red Hat's official container image repository.

# Lab 3
```bash
lab work-containers setup
sudo mkdir -p /var/local/mysql # create host folder to link to container data folder
# Apply the appropriate SELinux context to the host folder.
sudo chcon -R -t svirt_sandbox_file_t /var/local/mysql 
# Change the owner of the host folder to the mysql user (uid=27) and mysql group (gid = 27).
sudo chown -R 27:27 /var/local/mysql

# create & start container 
docker run --name mysql-1 \
-d -v /var/local/mysql:/var/lib/mysql/data \ #link host folder to container folder
-e MYSQL_USER=user1 -e MYSQL_PASSWORD=mypa55 \
-e MYSQL_DATABASE=items -e MYSQL_ROOT_PASSWORD=r00tpa55 \
rhscl/mysql-57-rhel7

docker ps
# Load the items database using the /home/student/DO180/labs/work-containers/db.sql script.
docker inspect -f '{{ .NetworkSettings.IPAddress }}' mysql-1 #return ip address of container
# load the database
mysql -uuser1 -h <CONTAINER_IP> -pmypa55 items \ 
< /home/student/DO180/labs/work-containers/db.sql
# Verify that the database was loaded.
mysql -uuser1 -h CONTAINER_IP -pmypa55 items -e "SELECT * FROM Item"

# Create 2nd mysql container that accesses same persisted data
docker run --name mysql-2 \
-d -v /var/local/mysql:/var/lib/mysql/data \
-p 13306:3306 \
-e MYSQL_USER=user1 -e MYSQL_PASSWORD=mypa55 \
-e MYSQL_DATABASE=items -e MYSQL_ROOT_PASSWORD=r00tpa55 \
rhscl/mysql-57-rhel7

docker ps

# Save the list of all containers (including stopped ones) to the /tmp/my-containers file.
docker ps -a > /tmp/my-containers

# Access the bash shell inside the container and verify that the items database and the Item table are still available. Confirm also that the table contains data.
docker exec -it mysql-2 /bin/bash
mysql -uroot
mysql> show databases;
mysql> use items;
mysql> show tables;
mysql> SELECT * FROM Item;

# Using the port forward, insert a new Finished lab row in the Item table.
mysql -uuser1 -h workstation.lab.example.com -pmypa55 -P13306 items #log into remote mysql db
MySQL[items]> insert into Item (description, done) values ('Finished lab', 1);

```
# Chapter 3 Summary
- Docker commands:
  - docker run: Create a new container.
  - docker ps: List containers.
  - docker inspect: List metadata about a container.
  - docker stop: Stop a container.
  - docker kill: Stop a container forcefully.
  - docker restart: Restart a stopped container.
  - docker rm: Delete a container.
- Container storage is said to be ephemeral, meaning its contents are not preserved after the container is removed.
- To work with persistent data, a folder from the host can be used.
- It is possible to mount a volume with the -v option in the docker run command.
- The docker exec command starts an additional process inside a running container.
- A port mapping can be used with the -p option in the docker run command.

# Lab 4

```bash
lab container-images-lab setup

# Start a new container using the Nginx image from the internal registry
docker run --name official-nginx -d -p 8080:80 registry.lab.example.com/nginx

# Log in to the container using the exec verb and update the content index.html file with DO180 Page.
docker exec -it official-nginx /bin/bash
echo 'DO180 Page' > /usr/share/nginx/html/index.html

# Stop the running container and commit your changes to create a new container image. Give the new image a name of do180/mynginx and a tag of v1.0
docker stop official-nginx
docker commit -a 'Your Name' -m 'Changed index.html page' official-nginx
docker images
docker tag d77b234dec1c do180/mynginx:v1.0

# Use the image tagged do180/mynginx:v1.0 to create a new container
docker run -d --name my-nginx -p 8280:80 do180/mynginx:v1.0

```

# Chapter 4 Summary

- Registries must be used to pull and push container images to private registries for internal use, or to public registries for outside consumption.
  - The Red Hat Software Collections Library (RHSCL) provides tested and certified images at registry.access.redhat.com.
  - Docker daemons support extra registries by editing the /etc/sysconfig/docker file and by adding new registries to the ADD_REGISTRY variable.
  - In order to support registries with self-signed certificates, add the registry to the INSECURE_REGISTRY variable of the /etc/sysconfig/docker configuration file.
  - Registries implement a RESTful API to pull, push, and manipulate objects. The API is used by the Docker daemon, but it can also be queried via tools such as curl.
  - To search for an image in a public registry, use the docker search command.
  - To search for an image in a private registry, use the docker-registry-cli command.
  - To pull an image from a registry, use the docker pull command.
  - Registries use tags as a mechanism to support multiple image releases.
- The Docker daemon supports export and import procedures for image files using the docker export, docker import, docker save, and docker load commands.
  - For most scenarios, using docker save and docker load command is the preferred approach.
- The Docker daemon cache can be used as a staging area to customize and push images to a registry.
- Docker also supports container image publication to a registry using the docker push command.
- Container images from a daemon cache can be removed using the docker rmi command.ful

# Chapter 5 Lab
```
vim Dockerfile

# base image
FROM rhel7:7.5

# maintainer details
MAINTAINER Damien Murphy <damurphy@redhat.com>

# set env var for port number
ENV PORT 8080

# add repos & run yum install and clean
ADD training.repo /etc/yum.repos.d/training.repo
RUN yum install -y httpd && \
yum clean all

# replace port value in apache config file and change ownership of files
RUN sed -ri -e '/^Listen 80/c\Listen ${PORT}' /etc/httpd/conf/httpd.conf \
&& chown -R apache:apache /etc/httpd/logs/ \
&& chown -R apache:apache /run/httpd/

# sets apache user to execute any RUN, CMD, or ENTRYPOINT commands
USER apache

# expose port on container
EXPOSE ${PORT}

# copy host folder to html folder in container
COPY ./src/ /var/www/html/

# start httpd
CMD ["httpd", "-D", "FOREGROUND"]

docker build -t do180/custom-apache .

docker images

docker run --name lab-custom-images -d -p 20080:8080 do180/custom-apache

curl 127.0.0.1:20080 #check container is serving properly
```

# Chapter 5 Summary

- The usual method of creating container images is using Dockerfiles.
- Dockerfiles provided by Red Hat or Docker Hub are a good starting point for creating custom images using a specific language or technology.
- The Source-to-Image (S2I) process provides an alternative to Dockerfiles. S2I spares developers the need to learn low-level operating system commands and usually generates smaller images.
- Building an image from a Dockerfile is a three-step process:
  - Create a working directory.
  - Write the Dockerfile specification.
  - Build the image using the docker build command.
- The RUN instruction is responsible for modifying image contents.
- The following instructions are responsible for adding metadata to images:
  - LABEL
  - MAINTAINER
  - EXPOSE
- The default command that runs when the container starts can be changed with the RUN and ENTRYPOINT instructions.
- The following instructions are responsible for managing the container environment:
  - WORKDIR
  - ENV
  - USER
- The VOLUME instruction creates a mount point in the container.
- The Dockerfile provides two instructions to include resources in the container image:
  - ADD
  - COPY


# Chapter 6 Lab

```bash 
oc new-app  php:5.6~http://services.lab.example.com/temps 
oc logs -f bc/temps
oc get pods -w #watches pods
oc expose svc/temps
oc get route/temps
curl temps-ocp.apps.lab.example.com
```

# Chapter 6 Summary

- The OpenShift command line client oc is used to perform the following tasks in an OpenShift cluster:
  - Logging in and out of an OpenShift cluster.
  - Creating, changing, and deleting projects.
  - Creating applications inside a project, including creating a deployment configuration from a container image, or a build configuration from application source and all associated resources.
  - Creating, deleting, inspecting, editing, and exporting individual resources such as pods, services, and routes inside a project.
  - Scaling applications.
  - Starting new deployments and builds.
  - Checking logs from application pods, deployments, and build operations.
- The OpenShift Container Platform organizes entities in the OpenShift cluster as objects stored on the master node. These are collectively known as resources. The most common ones are:
  - Pod
  - Label
  - Persistent Volume (PV)
  - Persistent Volume Claim (PVC)
  - Service
  - Route
  - Replication Controller (RC)
  - Deployment Configuration (DC)
  - Build Configuration (BC)
  - Project
- The oc new-app command can create application pods to run on OpenShift in many different ways. It can create pods from existing Docker images, from Dockerfiles, and from raw source code using the Source-to-Image (S2I) process.
- Source-to-Image (S2I) is a facility that makes it easy to build a container image from application source code. This facility takes an application's source code from a Git server, injects the source code into a base container based on the language and framework desired, and produces a new container image that runs the assembled application.
- A Route connects a public-facing IP address and DNS host name to an internal-facing service IP. While services allow for network access between pods inside an OpenShift instance, routes allow for network access to pods from users and applications outside the OpenShift instance.
- You can create, build, deploy and monitor applications using the OpenShift web console.


# Chapter 7 Lab

```bash
oc login -u admin -p redhat https://master.lab.example.com
oc new-project deploy
oc adm policy add-scc-to-user anyuid -z default #change security privileges for container to run
docker build -t do180/mysql-57-rhel7 #build mysql db container

# tag & push image to private registry
docker tag do180/mysql-57-rhel7 registry.lab.example.com/do180/mysql-57-rhel7
docker push registry.lab.example.com/do180/mysql-57-rhel7

docker build -t do180/quote-php . #build php-quote container

docker tag do180/quote-php registry.lab.example.com/do180/quote-php
docker push registry.lab.example.com/do180/quote-php

./create-pv.sh #create pv's
# oc create -f PV.json #create PV from PV template file 

oc create -f quote-php-template.json #create template from template file
# process template & create application from resources specified by template
oc process quote-php-persistent | oc create -f -
oc process -f quote-php-persistent.json | oc create -f - #above didn't seem to work so created from template file instead

# check pods are spinning up
oc get pods -w #this seemed flaky

oc expose svc quote-php #create a route
oc get route #get created route
curl http://quote-php-deploy.apps.lab.example.com #test app endpoint

# clean-up
oc delete project deploy
./delete-pv.sh #delete pv's
# oc delete pv pv0001 #delete pv by name
docker rmi -f $(docker images -q)hisr

```

# Chapter 7 Summary

- Containerized applications cannot rely on fixed IP addresses or host names to find services. Docker and Kubernetes provide mechanisms that define environment variables with network connection parameters.
- The user-defined docker networks allow containers to contact other containers by names. To be able to communicate with each other, containers must be attached to the same user-defined docker network, using --network option with the docker run command.
- Kubernetes services define environment variables injected into all pods from the same project.
- Kubernetes templates automate creating applications consisting of multiple pods interconnected by services.
- Template parameters define environment variables for multiple pods.


# Chapter 8 Lab

```bash
lab troubleshooting-lab setup
cd ~student/DO180/labs/troubleshooting-lab
vim Dockerfile
# add `COPY httpd.conf /etc/httpd/conf/` to Dockerfile to copy apache config to docker container
docker build -t do180/broken-httpd . #build image
docker run --name broken-httpd -d  -p 3000:80 -v /usr/bin:/usr/bin do180/broken-httpd #build and run container
docker logs broken-httpd #check logs of container
docker exec -it broken-httpd /bin/bash #ssh into running container
[root@9ef58f71371c /] cat /var/www/html/index.html #check page in container
ping materials.example.net #test url specified in page
docker build -t do180/updated-httpd . #fix html page & build new image
docker stop broken-httpd #stop faulty container
docker rm broken-httpd #delete faulty container
docker run --name updated-httpd -d -p 3000:80 -v /usr/bin:/usr/bin do180/updated-httpd #build new container from updated image & check if url ok now on updated page
docker stop updated-httpd #stop updated container
docker rm updated-httpd #delete updated container
docker rmi do180/broken-httpd do180/updated-httpd #delete images

```

# Chapter 8 Summary

- S2I images requires that security concerns must be addressed to minimize build and deployment issues.
- Development and sysadmin teams must work together to identify and mitigate problems with respect to S2I image creation process.
- Troubleshoot containers by using the oc port-forward command to debug applications as a last resource.
- OpenShift events provide low-level information about a container and its interactions. They can be used as a last resource to identify communication problems.