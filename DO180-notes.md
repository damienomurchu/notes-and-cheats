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
- Container images from a daemon cache can be removed using the docker rmi command.