# Step 6: Understanding Persistent Storage

## Manage data in Docker

By default all files created inside a container are stored on a writable container layer. This means that:

* The data doesn’t persist when that container no longer exists, and it can be difficult to get the data out of the container if another process needs it.
* A container’s writable layer is tightly coupled to the host machine where the container is running. You can’t easily move the data somewhere else.
* Writing into a container’s writable layer requires a storage driver to manage the filesystem. The storage driver provides a union filesystem, using the Linux kernel. This extra abstraction reduces performance as compared to using data volumes, which write directly to the host filesystem.

Docker has two options for containers to store files in the host machine, so that the files are persisted even after the container stops: volumes, and bind mounts. If you’re running Docker on Linux you can also use a tmpfs mount. If you’re running Docker on Windows you can also use a named pipe.

Keep reading for more information about these two ways of persisting data.

## Choose the right type of mount

No matter which type of mount you choose to use, the data looks the same from within the container. It is exposed as either a directory or an individual file in the container’s filesystem.

An easy way to visualize the difference among volumes, bind mounts, and tmpfs mounts is to think about where the data lives on the Docker host.

![](../images/lab2_understanding_persistent_storage_1.png)

* Volumes are stored in a part of the host filesystem which is managed by Docker (/var/lib/docker/volumes/ on Linux). Non-Docker processes should not modify this part of the filesystem. Volumes are the best way to persist data in Docker.
* Bind mounts may be stored anywhere on the host system. They may even be important system files or directories. Non-Docker processes on the Docker host or a Docker container can modify them at any time.
* tmpfs mounts are stored in the host system’s memory only, and are never written to the host system’s filesystem.

Above information is taken from [Docker's official documentation](https://docs.docker.com/storage/)

## Persisting a MySQL Database

Navigate to MySQL Docker Image on [DockerHub](https://hub.docker.com/_/mysql) and inspect usage.

1. Pull the MySQL container image from DockerHub.
```bash
   docker container pull mysql
 ```
 
 2. Inspect MySQL container image to learn for volume path.
 ```bash
   docker container inspect mysql
 ```
 
 3. Create a new container specifying the mount point to store the MySQL database data.
  ```bash
   docker container run -d --name persistent-mysql -v /root/mysql:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=admin -e MYSQL_USER=user1 -e MYSQL_PASSWORD=password -e MYSQL_DATABASE=items mysql
 ```
 
