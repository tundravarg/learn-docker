# Docker Tutorial

* https://docs.docker.com/get-started/
* https://docs.microsoft.com/ru-ru/visualstudio/docker/tutorials/your-application



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