---
layout: post
title: docker learning cheat sheet
comments: true
description: "docker learning"
keywords: "docker"
---

1. Orientation

   A container is launched by running an image, and a container is a runtime instance of an image.

   ```shell
   ## List Docker CLI commands
   docker
   docker container --help

   ## Display Docker version and info
   docker --version
   docker version
   docker info

   ## Execute Docker image
   docker run hello-world

   ## List Docker images
   docker image ls

   ## List Docker containers (running, all, all in quiet mode)
   docker container ls
   docker container ls --all
   docker container ls -aq
   ```


2. Containers

   These portable images are defined by `Dockerfile`. `Dockerfile` defines what goes on in the environment inside your container.

   image / container involved command:

   ```shell
   docker build -t friendlyhello .  # Create image using this directory's Dockerfile
docker run -p 4000:80 friendlyhello  # Run "friendlyhello" mapping port 4000 to 80
docker run -d -p 4000:80 friendlyhello         # Same thing, but in detached mode
docker container ls                                # List all running containers
docker container ls -a             # List all containers, even those not running
docker container stop <hash>           # Gracefully stop the specified container
docker container kill <hash>         # Force shutdown of the specified container
docker container rm <hash>        # Remove specified container from this machine
docker container rm $(docker container ls -a -q)         # Remove all containers
docker image ls -a                             # List all images on this machine
docker image rm <image id>            # Remove specified image from this machine
docker image rm $(docker image ls -a -q)   # Remove all images from this machine
docker login             # Log in this CLI session using your Docker credentials
docker tag <image> username/repository:tag  # Tag <image> for upload to registry
docker push username/repository:tag            # Upload tagged image to registry
docker run username/repository:tag                   # Run image from a registry
   ```

   

3. Services

   Compose is a tool for defining and running multi-container Docker applications.
   With Compose, you use a YAML file to configure your application’s services.

   A `docker-compose.yml` file is a YAML file that defines how Docker containers
   should behave in production.
   
   After defining the yaml
   
   ```shell
   docker swarm init
   ```
   
   ```shell
   docker stack deploy -c docker-compose.yml <name>
   ```
   
   ```shell
   docker service ls
   ```
   
   ```shell
   docker service ps <hash | name>
   ```
   
   problem spotted: a single container is listening to the port 4000, then turn on the services, no port conflict will raise and only the container in the service will respond to the request from 4000
   
   ```shell
   docker stack ls
   ```
   
   ```shell
   docker stack ps <hash | name>
   ```
   
   scale the app
   
   You can scale the app by changing the `replicas` value in `docker-compose.yml`,
   saving the change, and re-running the `docker stack deploy` command:
   
   ```shell
   docker stack deploy -c docker-compose.yml [old stack name]
   ```
   
   take down the swarm
   
   ```shell
   docker stack rm [name]
   ```
   
   or
   
   ```shell
   docker swarm leave --force
   ```

4. swarm

   A swarm is a group of machines that are running Docker and joined into a cluster. The machines in a swarm can be physical or virtual. After joining a swarm, they are referred to as **nodes**.

   Swarm managers are the only machines in a swarm that can execute your commands, or authorize other machines to join the swarm as **workers**.

   run `docker swarm init` to enable swarm mode and make your current machine a swarm manager, then run `docker swarm join` on other machines to have them join the swarm as workers.

   view the nodes the nodes in this swarm:

   ```shell
   docker node ls
   ```

   You can join any machine, physical or virtual, to this swarm, using the same `docker swarm join` command, and capacity is added to your cluster. Just run `docker stack deploy` afterwards, and your app can take advantage of the new resources.

5. stacks

   A stack is a group of interrelated services that share dependencies, and can be orchestrated and scaled together.
   
   for log checking
   
   ```shell
   journalctl -u docker.service -n 100
   ```
   
   