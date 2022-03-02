
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