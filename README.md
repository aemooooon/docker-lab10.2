# Docker-lab10.2

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
1. Make a directory 'flaskapp' to server as a build contenxt for the Python/Flask application image. In that directory, create a dockerfile with the following contents:
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
2. clone the GitHub repository [aemooooon/lab-4-app]('https://github.com/aemooooon/lab-4-app') into your context.
3. build container image with the tag: `docker build -t="aemooooon/flaskapp" .`
4. Run command `docker run -d --rm --name flaskapp -p 5000:5000 aemooooon/flaskapp` to test.
The application should works now, but for a production deployment we need to place it behind a reverse proxy server. For this, we will need a second container. Shut down flaskapp container.

### Create an Nginx Container

### Create a Docker Network
