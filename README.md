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

> sudo docker network create --driver bridge network-name

* Run a container and place it on the specified network

> sudo docker run -dit --name container-name --network network-name ubuntu /bin/bash

* Inspect network information 

> sudo docker network inspect networkn-name

