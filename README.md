# Docker from Virtualization

## Docker Introducation
* Installation

> sudo apt-get install docker.io

By defaut, the docker commands are only usable by root. The docker package created a docker group, but any member of that group can run commands without sudo. So add you `user` to the docker group with the command `sudo adduser user docker`.

* Check the docker server running info with `docker info`
```bash
Containers: 24
 Running: 5
 Paused: 0
 Stopped: 19
Images: 28
Server Version: 18.09.7
Storage Driver: overlay2
 Backing Filesystem: extfs
 Supports d_type: true
 Native Overlay Diff: true
Logging Driver: json-file
Cgroup Driver: cgroupfs
Plugins:
 Volume: local
 Network: bridge host macvlan null overlay
 Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
Swarm: inactive
Runtimes: runc
Default Runtime: runc
Init Binary: docker-init
containerd version:
runc version: N/A
init version: v0.18.0 (expected: fec3683b971d9c3ef73f284f176672c44b448662)
Security Options:
 apparmor
 seccomp
  Profile: default
Kernel Version: 4.4.0-87-generic
Operating System: Ubuntu 16.04.6 LTS
OSType: linux
Architecture: x86_64
CPUs: 1
Total Memory: 992.3MiB
Name: placeholder-0
ID: JQWU:UX5K:VCKI:45FF:GUUA:2J3H:XE4F:4BCL:PWXM:TRBB:764R:CHGY
Docker Root Dir: /var/lib/docker
Debug Mode (client): false
Debug Mode (server): false
Username: aemooooon
Registry: https://index.docker.io/v1/
Labels:
Experimental: false
Insecure Registries:
 127.0.0.0/8
Live Restore Enabled: false

WARNING: No swap limit support
```

## Docker Network
1. 不同的网络之间相互隔离，无法访问。
2. 如果是用户自定义的网络，我们不仅可以通过IP地址访问，同时也能通过主机名访问。

* List Networks

> sudo docker network ls

```bash
NETWORK ID          NAME                DRIVER              SCOPE
312817f6163c        app                 bridge              local
cee45c66cd9f        bridge              bridge              local
104d20355301        host                host                local
ea2782d9ba8c        lab-net             bridge              local
5f98aad42051        none                null                local
```
* Create an additional bridged network

> sudo docker network create --driver bridge network-name # we simply choose bridge as driver

* Run a container and place it on the specified network

> sudo docker run -dit --name container-name --network network-name ubuntu /bin/bash

* Inspect network information 

> sudo docker network inspect networkn-name


## Run two container with together by one network
We are going to create a simple service using a Python/Flask application in one container and an Nginx reverse proxy server in a second container. Then create a Docker network to connect the two containers.

### Create a Flask Application Container
1. Make a directory 'flaskapp' to server as a build context for the Python/Flask application image. In that directory, create a dockerfile with the following contents:
```bash
FROM ubuntu:16.04
LABEL updated_on="2019-10-18 09:00"
RUN apt-get update
RUN apt-get -y upgrade
RUN apt-get -y install python3 python3-setuptools python3-pip gunicorn3
RUN update-alternatives --install /usr/bin/python python /usr/bin/python3 10

COPY lab-4-app /flaskapp
WORKDIR /flaskapp
RUN pip3 install -r requirements.txt

EXPOSE 5000
ENTRYPOINT "./startup.sh"
```
2. Clone the GitHub repository [aemooooon/lab-4-app]('https://github.com/aemooooon/lab-4-app') into your context.
3. Build container image with the tag: `docker build -t="aemooooon/flaskapp" .`
4. Run command `docker run -d --rm --name flaskapp -p 5000:5000 aemooooon/flaskapp` to test.
The application should works now, but for a production deployment we need to place it behind a reverse proxy server. For this, we will need a second container. Shut down flaskapp container.

### Create an Nginx Container
1. Make a directory 'nginx' to serer as a build context. Place a dockerfile in it with the following:
```bash
FROM nginx:1.13
COPY flaskapp.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
```
2. Add flaskapp.conf file to the context with the following:
```bash
resolver 127.0.0.11 valid=1s;
server {
  set $alias "flaskapp";
  location / {
    proxy_set_header Host $host;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_pass http://$alias:5000;
  }
  listen 80;
}
This will cause nginx to send proxy requests to a flaskapp host.
```
3. Build this image with the tag: `docker build -t="aemooooon/nginx" .`
4. Now we can run two containers based on our images with the following commands.
```bash
docker run -d --rm --name flaskapp aemooooon/flaskapp
docker run -d --rm --name nginx -p 8080:80 aemooooon/nginx
```
we can see does working, but not working together.

### Create a Docker Network
We want to place our containers on their own isolated network. The Flask application container does not need to be reachable by anything but our nginx container. Create our network with the command:
```bash
docker network create app

#then

docker run -d --rm --name flaskapp --network app aemooooon/flaskapp
docker run -d --rm --name nginx --network app -p 8080:80 aemooooon/nginx
```
Note that it doesn't matter in which order you start the containers.
