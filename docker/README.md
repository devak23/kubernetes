
## Learn Docker

### Chap 2
- Hello World
    ```
    docker container run diamol/ch02-hello-diamol
    ```
- Container has no OS. It shares the OS from the native OS. A VM has its own OS.

- Runs an interactive terminal session
    ```
    docker container run --interactive --tty diamol/base
    ```
- Show top
    ```
    docker container top <id>
    ```
- Show logs
    ```
    docker container logs <id>
    ```
- Inspects a container
    ```
    docker container inspect <id>
    ```
- Lists all containers 
    ```
    docker container ls --all
    ```
- Allowing container to run in background
    ```
    docker container run --detach --publish 8088:80 diamol/ch02-hello-diamol-web
    ```
    - --detach--Starts the container in the background and shows the container ID
    - --publish--Publishes a port from the container to the computer

- Provides stats for a specific container id
    ```
    docker container stats <id>
    ```
- Removes all containers
    ```
    docker container rm --force $(docker container ls --all --quiet)    
    ```

### Chap 3
- Pulls a docker image from the repository
    ```
    docker image pull diamol/ch03-web-ping
    ```
- Run the docker image in a detach mode of the image just downloaded. Use -d for running the container in the background and --name to provide a friendly name to the running container
    ```
    docker container run -d --name web-ping diamol/ch03-web-ping
    ```
- Tailing the logs of the container
    ```
    docker container logs -f web-ping
    ```
- Docker images may be packaged with a default set of configuration values for the application, but you should be able to provide different configuration settings when you run a container. Environment variables are a very simple way to achieve that.
    ```
    docker rm -f web-ping
    docker container run --env TARGET=google.com diamol/ch03-web-ping
    ```
The host computer has its own set of environment variables too, but they’re separate from the containers. Each container only has the environment variables that Docker populates.

- web-ping:
```
FROM diamol/node
ENV TARGET="blog.sixeyed.com"
ENV METHOD="HEAD"
ENV INTERVAL="3000"
WORKDIR /web-ping
COPY app.js .
CMD ["node", "/web-ping/app.js"]
```

- Search docker images using wilcard
    ```
    docker image ls 'w*'
    ```
- You can change the location of the destination using the environmental parameters supplied
    ```
    docker container run -e TARGET=yahoo.com -e INTERVAL=5000 mywebping
    ```
- You can look at the history of a docker image to see how the image was layered
    ```
    docker image history mywebping
    ```
    A docker image is a logical collection of image layers. Layers are the files that are physically stored in the Docker engine's cache. Image layers can be shared across different images. If the image layer is shared around, they cannot be modified else a change in one image would cascade to all the other images as well. Docker enforces that by making the image layer read-only. You can take advantage of that fact and make your builds faster by optimizing your Dockerfiles.

    Every docker instruction results in an image layer, but if the instruction does not change between builds and if the contents remain the same, Docker knows it can use the previous layer from the cache. Docker calculates whether the input has a match in the cache by generating a hash. The hash is made up of Dockerfile instruction and the contents of the files being copied. If there is no match for the hash, Docker executes the instruction and that breaks the cache. When that happens, Docker will execute all the instructions that follow, even if they haven't changed.





