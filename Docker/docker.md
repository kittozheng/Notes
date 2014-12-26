#Docker
Build, Ship and Run Any App, Anywhere

##Install

* Create `docker_install.sh` and run it as root

```
#!/bin/bash

# check https transport exist, if not then install it
if [ ! -e /usr/lib/apt/methods/https ]; then
	apt-get update
	apt-get install -y apt-transport-https
fi

# Add Docker source to APT source list
echo deb https://get.docker.io/ubuntu docker main > /etc/apt/sources.list.d/docker.list

# import secret key
apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9

# install docker
apt-get update
apt-get install -y lxc-docker

# create map docker cmd
ln -sf /usr/bin/docker /usr/local/bin/docker

exit

```

* Add execute permission to `docker_install.sh` script and run it

```
$ chmod +x docker_install.sh
$ ./docker_install.sh
```

##DokcerUser

* check docker version

```
$ docker --verison
```

* create docker user

```
$ sudo mkdir /home/<username>
$ sudo useradd -d /home/<username>/ -s /bin/bash <username>
$ sudo gpasswd -a <username> docker     
$ sudo passwd <username>
```

## Docker Image Management

* download images

```
$ docker search ubuntu12.04
$ docker search ubuntu14.04

$ docker pull chug/ubuntu12.04x64 
$ docker pull chug/ubuntu14.04x64 
```

* cmd

```
#remove image
$ docker  rmi <image_name>

#show containers
$  docker ps
$ docker ps -a

#remove container
$ docker rm <docker_container_id>
```

* Run Docker Image

```
#show docker images 
$ docker images

#run the chosen docker image from show images
docker run -t  -i <REPOSITORY>  /bin/bash

```