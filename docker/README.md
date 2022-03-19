
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


There are 7 instructions in the file shown above but it can still be optimized. For example:
- The CMD command doesn't have to be at the end of the file. It can be anywhere in the file (after FROM) and still yield the same result.
- The ENV command can be used to set multiple enviroment variables so 3 separate ENV instructions can be combined.

```
FROM diamol/node
CMD ["node", "/web-ping/app.js"]
ENV TARGET="blog.sixeyed.com" \
	METHOD="HEAD" \
	INTERVAL="3000" \
WORKDIR /web-ping
COPY app.js .
```

### Chap3
- Here we have a dockerfile with 2 stages in it. The sample application is a java springboot application. That will be built by a docker image and then the runtime will be done by another one. The code is as follows:
```
# Build Application
FROM diamol/maven AS builder
WORKDIR /usr/src/iotd
COPY pom.xml .
RUN mvn -B dependency:go-offline

COPY . .
RUN mvn package

# Package Application 
FROM diamol/openjdk
WORKDIR /app
COPY --from=builder /usr/src/iotd/target/oitd-service-0.1.0.jar .

EXPOSE 80
ENTRYPOINT ["java", "jar", "/app/iotd-service-0.1.0.jar"]
```
The first stage is the builder as mentioned before. HEre is what happens in the builder stage:

- It uses diamol/maven image as a base. That image has openJDK Java development kit installed as well as maven build tool
- The builder stage starts by creating a working directory in the image and copying the pom.xml into it
- The first RUN statement executes the maven command fetching all the application dependencies. This is an expensive operation and hence it has its own step to make use of the Docker layer caching. If there are new dependencies, the XML file will change and the step will run, else cache will be used.
- COPY . . means copy all the files and directories from the location where the docker build is running, into the working directory in the image.
- The last step of the builder is to run the mvn package, which compiles and packages the application into a jar file.

If the builder stage finishes successfully, Docker goes on to execut the final stage whcih produces the image:
- Loads the diamol/openjdk package which is a Java 11 runtime, but none of the maven build tools are included.
- Next it creates a working directory of /app
- Then it copies jar file from the builder stage into the working directory
- Next it exposes port 80 to listen to incoming requests
- The ENTRYPOINT is an alternative to CMD instruction. It tells Docker what to do when a container is started from an image.

Docker containers need to communicate with each other. Containers access each other across a virtual network using a virtual IP address that Docker allocates when it creates a network. You can create and manage networks through the command line:

```
docker network create nat
```

Now lets run the above application in a detached mode accessing the port 80 inside it and connecting the container to the nat network.
```
docker container run --name iotd -d -p 9000:80 --network nat iotd:v1
```


