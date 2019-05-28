###Docker Learning

1. Orientation

   A container is launched by running an image, and a container is a runtime instance of an image.

   ```shell
   docker image ls
   ```

   ```shell
   docker container ls -a
   ```

2. Containers

   These portable images are defined by `Dockerfile`. `Dockerfile` defines what goes on in the environment inside your container.

   image / container involved command:

   ```shell
   docker build --tag=friendlyhello .
   ```

   ```shell
   docker run -d -p 4000:80 friendlyhello
   ```

   ```shell
   docker container stop [hash]
   ```

   ```shell
   docker container prune
   ```

   ```shell
   docker container rm [hash | name]
   ```

   ```shell
   docker image rm [hash]
   ```

   ```shell
   docker exec -it [hash] bash
   ```

   

   docker hub related command:

   ```shell
   docker login
   ```

   ```shell
   docker tag [image] [username]/[repository]:[tag]
   ```

   ```shell
   docker push [username]/[repository]:[tag]
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
   docker stack deploy -c docker-compose.yml [name]
   ```
   
   ```shell
   docker service ls
   ```
   
   ```shell
   docker service ps [hash | name]
   ```
   
   problem spotted: a single container is listening to the port 4000, then turn on the services, no port conflict will raise and only the container in the service will respond to the request from 4000
   
   ```shell
   docker stack ls
   ```
   
   ```shell
   docker stack ps [hash | name]
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

   ​	Swarm managers are the only machines in a swarm that can execute your commands, or authorize other machines to join the swarm as **workers**.

   ​	run `docker swarm init` to enable swarm mode and make your current machine a swarm manager, then run `docker swarm join` on other machines to have them join the swarm as workers.

   ​	view the nodes the nodes in this swarm:

   ```shell
   docker node ls
   ```

   

