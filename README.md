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

    docker run -d -e MYSQL_ROOT_PASSWORD=P4sSw0rd0! -v ~/my_executables/docker/mysql_data:/var/lib/mysql --name app-database mysql    ```



