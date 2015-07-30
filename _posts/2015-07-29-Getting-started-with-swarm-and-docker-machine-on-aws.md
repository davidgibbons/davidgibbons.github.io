---
layout: post
category : devops
tagline: "Swarm+Docker-machine+AWS in a few commands"
tags : [ devops, docker, aws ]
---


## set some shell variables
```bash
export AWS_ACCESS_KEY_ID=YOUR_ACCESS_KEY_HERE
export AWS_SECRET_ACCESS_KEY=YOUR_SECRET_KEY_HERE
export AWS_DEFAULT_REGION=us-west-2
```

## Use a local docker container to generate a swarm token
```bash
docker run swarm create
```

This will return a hash as the output e.g.:
fe0cc96a72cf04dba8c1c4aa79536ec3

## Set this as a shell env var:
```bash
export SWARM_TOKEN=fe0cc...
```

## Launch the swarm manager in AWS:
```bash
docker-machine create \
        -d amazonec2 \
        --swarm \
        --swarm-master \
        --swarm-discovery token://$SWARM_TOKEN \
        swarm-master
```

# Launch a few nodes:
```bash
docker-machine create \
    -d amazonec2 \
    --swarm \
    --swarm-discovery token://$SWARM_TOKEN \
    swarm-agent-01

docker-machine create \
    -d amazonec2 \
    --swarm \
    --swarm-discovery token://$SWARM_TOKEN \
    swarm-agent-00
```

# Change the active docker server to the swarm manager:
```bash
eval $(docker-machine env swarm-manager)
```

# Look at the swarm:
```bash
docker info
```

# run a docker container on the swarm:
```bash
docker run hello-world
```

A further test, I started up a minecraft server:
```bash
$ docker run -e EULA=TRUE  -d -p 25565:25565 itzg/minecraft-server
4baa76223f7c4b8a99aa7fe9201a4cfeb03e2a870fda70c213e7c356d4c82a7f

$ docker ps
CONTAINER ID        IMAGE                   COMMAND             CREATED             STATUS              PORTS                            NAMES
4baa76223f7c        itzg/minecraft-server   "/start"            6 seconds ago       Up 6 seconds        52.26.250.197:25565->25565/tcp   swarm-agent-01/backstabbing_fermi

```


This was basically following the guide on:
http://docs.docker.com/swarm/install-w-machine/
also using this blog post:
http://networkstatic.net/docker-machine-provisioning-on-aws/


