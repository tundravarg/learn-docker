# Docker Tutorial

* https://docs.docker.com/get-started/
* https://docs.microsoft.com/ru-ru/visualstudio/docker/tutorials/your-application



## Table of Contents

* [1. Getting Started](#1-Getting-Started)
* [2. Sample Application](#2-Sample-Application)
* [3. Update the Application](#3-Update-the-Application)
* [4. Share the Application](#4-Share-the-Application)
* [5,6. Persist the DB](#56-Persist-the-DB)
* [7. Multi Container Apps](#7-Multi-Container-Apps)
* [8. Use Docker Compose](#8-Use-Docker-Compose)
* [Notes](#Notes)



## 1. Getting started


### Install on Linux Mint

https://docs.docker.com/engine/install/ubuntu

Modify step "... set up the stable repository ...":

    1. Check your Mint version: `cat /etc/os-release`

    2. Go to https://linuxmint.com/download_all.php and find matching Ubuntu release name

    3. Put it (my version is "focal") into this command (from installation instructions):

    ```
    echo \
    "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu focal stable" \
    | sudo tee /etc/apt/sources.list.d/docker.list
    ```


### Manage Docker as a non-root user

https://docs.docker.com/engine/install/linux-postinstall/

```sh
sudo groupadd docker
sudo usermod -aG docker $USER
```

Reboot or `newgrp docker`.

Check: `docker run hello-world`.




## 2. Sample application


```sh
docker build -t getting-started .
```

```sh
docker run -dp 3333:3000 getting-started
```

*   -d - “detached” mode (in the background).
*   -p - mapping between the host’s port 3333 to the container’s port 3000.



## 3. Update the application


```sh
docker ps
docker stop <the-container-id>
docker rm <the-container-id>
docker rm -f <the-container-id>
```

See: "Remove stopped containers" below.



## 4. Share the application


https://hub.docker.com/

1. Rename:

    ```sh
    docker tag image-name your-user-name/getting-started
    ```

2. Push:

    ```sh
    docker login -u your-user-name
    docker push your-user-name/image-name
    ```

Example:

```sh
docker tag getting-started tumanser/tutorial
docker login -u tumanser
docker push tumanser/tutorial
```

Examine you repo on hub.docker.com.



## 5,6. Persist the DB


```sh
docker run -d ubuntu bash -c "shuf -i 1-10000 -n 1 -o /data.txt && tail -f /dev/null"
```

```sh
docker exec <container-id> cat /data.txt
```

### Named Volumes

```sh
docker volume create todo-db
docker volume inspect todo-db
```

```sh
docker run -dp 3000:3000 -v todo-db:/etc/todos getting-started
```

### Bind Mounts

```sh
docker run -v host-path:container-path ...
```

Example:

```sh
docker run \
    -dp 3000:3000 \
    -w /app \
    -v "$(pwd):/app" \
    node:12-alpine \
    sh -c "yarn install && yarn run dev"
```

Print logs

```sh
docker logs -f <container-id>
```



## 7. Multi container apps


## Create a named network

```sh
docker network create <name>
```

```sh
docker run \
    -d \
    --network todo \
    --network-alias mysql \
    -v todo-mysql-data:/var/lib/mysql \
    -e MYSQL_ROOT_PASSWORD=secret \
    -e MYSQL_DATABASE=todos \
    mysql:5.7
```

Arguments:
* -d              - Detached mode
* --network       - Use network created by `docker network create`
* --network-alias - Host name in the network
* -v              - Bind volume
* -e              - Set environment variable

Notes:
* Volume `todo-mysql-data` will be created automatically.
* If MySQL isn't initialized, ${MYSQL_ROOT_PASSWORD} will be used as password
    and ${MYSQL_DATABASE} database will be created.
    If MySQL was initialized, nothing changes.
* You can't connect to MySql in second container on this image.

## Test the Network:

```sh
docker run -it --network todo-app nicolaka/netshoot
dig mysql
```

## Run ToDo Application:

```sh
docker run \
    -d \
    -p 3000:3000 \
    -w /app
    -v "$(pwd)/todo/app:/app" \
    --network todo \
    -e MYSQL_HOST=mysql \
    -e MYSQL_USER=root \
    -e MYSQL_PASSWORD=secret \
    -e MYSQL_DB=todos \
    node:12-alpine \
    sh -c "yarn install && yarn run dev"
```

## Test

Connect to DB container and run `mysql -p`.

```sql
SHOW databases;
USE todos;
SELECT * FROM todo_items;
```

Run the UI: `http:localhost:3000`, make changes and look at the changes in DB.



## 8. Use Docker Compose


TODO



## Notes


CLI Reference: https://docs.docker.com/engine/reference/commandline/docker/


### List images

```sh
docker image ls
docker images
```

Filter by name and tag:

```sh
docker image ls --filter reference=getti*:la*
```

Show unused images:

```sh
docker image ls --filter dangling=true
```

https://devconnected.com/how-to-list-docker-images/


### Remove unused images:

```sh
docker rmi `docker images -q --filter dangling=true`
```


### Cleanup redundant layers

TODO


### list running containers

```sh
docker container ls
docker ps
```

All containers:

```sh
docker ps -a
```

Stopped containers:

```sh
docker ps --filter status=exited
```


### Remove stopped containers

```sh
docker rm `docker ps -q --filter status=exited`
```


### Run commands in the container

```sh
docker exec <container-id> <comman>
```

Run interactive bash int the container:

```sh
docker exec -it <container-id> bash
```
