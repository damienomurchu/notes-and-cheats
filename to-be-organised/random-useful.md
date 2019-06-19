# https://stackoverflow.com/questions/30604846/docker-error-no-space-left-on-device#37287054

Next change the storage location with a -g option on the docker daemon or by editing /etc/default/docker and adding the -g option to DOCKER_OPTS. -g specifies the location of the "Docker runtime" which is basically all the stuff that Docker creates as you build images and run containers. Choose a location with plenty of space as the disk space used will tend to grow over time. If you edit /etc/default/docker, you will need to restart the docker daemon for the change to take effect.

Now you should be able to create a new image (or pull one from Docker Hub) and you should see a bunch of files getting created in the directory you specified with the -g option.

