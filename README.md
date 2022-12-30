# Docker Labs

## lab 1

### problem 1

- Run the container hello-world

``` bash
docker run hello-world

```

- Check the container status

``` bash
docker ps -a
```

- Start the stopped container

``` bash
docker start 6d8 #containerID

```

- Remove the container

``` bash
docker rm 6d8 #containerID
```

- Remove the image

``` bash
docker image rm 4917 #imageID

```

### problem 2

- Run container centos or ubuntu in an interactive mode

``` bash
docker run -it ubuntu 
```

- Run the following command in the container “echo docker ”

``` bash
ahmednasr@nasr-pc:~/my_executables/docker$ docker run -i ubuntu 
echo docker
docker
```

- Open a bash shell in the container and touch a file named hello-docker

``` bash
ahmednasr@nasr-pc:~/my_executables/docker$ docker run -it ubuntu 
root@cadcf39b93d6:/# touch hello-docker
root@cadcf39b93d6:/# ls -l hello-docker 
-rw-r--r-- 1 root root 0 Dec 28 11:20 hello-docker
root@cadcf39b93d6:/# 
```

- Stop the container and remove it. Write your comment about the file hello-docker

``` bash
docker stop cad #ubuntu container
docker rm cad #ubuntu container
#hello-docker will be removed
```

- Remove all stopped containers

``` bash
docker rm -f $(docker ps -aq)
```

### problem 3

- Run a container nginx with name nginx and attach a volume to the container

``` bash
docker run -it -v myVolume:/usr/share/nginx/html -p 8090:80 --name nginx1 nginx bash
```

- Volume for containing static html file

``` bash
root@17fed2f8c075:/# cd /usr/share/nginx/html/
root@17fed2f8c075:/usr/share/nginx/html# echo "Nasr editing in nginx" > index.html 
```

- Remove the container

``` bash
docker rm nginx1 
```

- Run a new container with the following:
  - Attach the volume that was attached to the previous container

    ``` bash
        docker run -it -v myVolume:/usr/share/nginx/html -p 9898:80 --name nginx2 nginx
    ```

  - Map port 80 to port 9898 on you host machine

    ``` bash
        docker run -it -v myVolume:/usr/share/nginx/html -p 9898:80 --name nginx2 nginx
    ```

  - Access the html files from your browser

    ``` bash
        docker run -it -v myVolume:/usr/share/nginx/html -p 9898:80 --name nginx2 nginx
    ```

### problem 4

- Run the image nginx again without attaching any volumes

    ``` bash
    docker run -it -p 9898:80 --name nginx3 nginx bash
    ```

- Add html static files to the container and make sure they are accessible

    ``` bash
    root@b0f420470311:/# cd /usr/share/nginx/html/
    root@b0f420470311:/# echo "Nasr working on problem 4 " > index.html 
    ```

- Commit the container with image name my nginx

    ``` bash
    docker commit nginx3 my-nginx
    docker run -it -p 9898:80 my-nginx
    ```

- Create a dockerfile for ngnix and build the image from this dockerfile

    ``` bash
    #in folder containing Dockerfile and index.html
    docker build -t mynginx2 .
    docker run -it -p 9090:80 mynginx2
    ```

### problem 5

- Create a volume called mysql_data, then deploy 
  a MySQL database called app-database. Use the
  mysql latest image, and use the -e flag to
  set MYSQL_ROOT_PASSWORD to P4sSw0rd0!.
  Mount the mysql_data volume to /var/lib/mysql.
  The container should run in the background.

    ``` bash
    docker run -d -e MYSQL_ROOT_PASSWORD=P4sSw0rd0! -v ~/my_executables/docker/mysql_data:/var/lib/mysql --name app-database mysql    
    ```

## lab 2

### problem 1

- Create your own nginx docker image based on ubuntu “NEVER USE FROM nginx”
  - Install nginx
  - Two index.html one as file and another as .tar "/var/www/html"
  - Expose
  - Start
  - Port mapping

  ``` bash
  #Dockerfile using index.html
  FROM ubuntu:20.04
  RUN apt-get update && apt-get install nginx -y
  EXPOSE 80
  ADD index.html /var/www/html
  CMD ["nginx", "-g", "daemon off;"]
  #Dockerfile using var.tar
  FROM ubuntu:20.04
  RUN apt-get update && apt-get install nginx -y
  EXPOSE 80
  ADD index.tar /var/www/html
  RUN command
  CMD ["nginx", "-g", "daemon off;"]
  #terminal 
  docker build -t custom-nginx:1.0 .
  docker run -it -p 7070:80 --name mynginx11 custom-nginx:1.0

  ```

### problem 2

- Create react app docker container "using single stage, Multi-Stage Dockerfile"

  ``` bash
  # Single stage:
  FROM node:18.12.1-alpine
  RUN apk update 
  RUN apk add nodejs
  RUN apk add npm 
  RUN npx create-react-app my-app
  WORKDIR /my-app
  CMD ["npm", "start"]
  EXPOSE 3000

  # Multi stage
  # Build source code stage
  FROM node:alpine3.16 As build
  WORKDIR /app
  COPY package*.json ./
  RUN npm install
  COPY . .
  RUN npm run build

  # Deploy stage
  FROM nginx:alpine
  COPY --from=build /app/build /usr/share/nginx/html
  EXPOSE 80
  ENTRYPOINT [ "nginx","-g","daemon off;" ]
  ```

### problem 3

- What is the rest of Docker Networks ? “Name and Definition”

  - macvlan: This network type allows you to assign a MAC address to a container and connect it directly to the physical network. This allows the container to appear as a physical device on the network and be assigned a unique IP address by the network's DHCP server.

  - network_mode: service:<service name>: This network type allows you to connect a container to the network of an existing Docker service. The container can communicate with other containers in the service's network using their service names.

  - network_mode: container:<container name>: This network type allows you to connect a container to the network of an existing Docker container. The container can communicate with other containers in the same network using their container names.

  - network_mode: host: This network type allows you to connect a container directly to the host machine's network interface, similar to the host network type. However, unlike the host network type, which creates a new network namespace for the container, the network_mode: host network type shares the host machine's network namespace with the container.
   
  - user-defined networks: In addition to the built-in network types, you can also create your own user-defined networks in Docker. You can create a user-defined network using the docker network create command and then use the --network flag to connect a container to the network.

### problem 4

- Create your bridge network, two containers from ubuntu image with different names and try to ping each other using NAME.

  ``` bash
  docker network create nasr-network
  docker run -id --name ubuntu1 --network nasr-network ubuntu
  docker run -id --name ubuntu2 --network nasr-network ubuntu
  docker exec -it ubuntu1 bash
  # inside ubuntu1 & ubuntu2
    apt update
    apt install inetutils-ping
    ping ubuntu2 #ping ubuntu1 from ubuntu2
  ```

## lab 3

### problem 1

- Convert the created react app multi-stage docker image into compose format

``` bash
  # Dockerfile
  # Build source code stage
  FROM node:alpine3.16 As build
  WORKDIR /app
  COPY package*.json ./
  RUN npm install
  COPY . .
  RUN npm run build

  #deploy stage
  FROM nginx:alpine
  COPY --from=build /app/build /usr/share/nginx/html
  EXPOSE 80
  ENTRYPOINT [ "nginx","-g","daemon off;" ]


  # docker-compose.yml
  version: '3.8'
  services:
    my-react-container:
      container_name: my-react-container
      build:
        context: .
        dockerfile: Dockerfile
      image: my-react-app:1.0
      ports: 
        - "3388:80"
```

### problem 2

- Create flask app to count number of visits to browser:
  - Create new directory called flask then add app.py and requirements.txt files
  - Create Dockerfile for the python app
  - Create docker-compose for the app and use Redis as temp DB.

``` bash
# Dockerfile
  FROM python:3.8-alpine
  WORKDIR /app
  COPY requirements.txt .
  RUN pip install -r requirements.txt 
  COPY . .
  CMD [ "python","app.py" ]

# docker-compose.yml
  version: '3.8'
  services:
    redis: 
      image: redis:alpine
    flask-app:
      build: 
        context: .
        dockerfile: Dockerfile
      image: flask-app-img
      ports:
        - 8888:8000
      depends_on:
        - redis    
 


```
