# ocker-lab10.2

```bash

# -d option, so we have to explicitly attach to the container 
# if we want to work in the shell we launched.
docker run -dit --name lab11a ubuntu /bin/bash 

docker network ls  # list network
docker network create --driver bridge lab-net # create a bridge network named 'lab-net'
docker network inspect bridge # inspect network named 'bridge'


apt update && apt install iputils-ping # install ping tool on ubuntu


```
