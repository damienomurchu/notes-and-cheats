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