# Docker Tutorial

* https://docs.docker.com/get-started/
* https://docs.microsoft.com/ru-ru/visualstudio/docker/tutorials/your-application



## 0. Setup


### Linux Mint

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
