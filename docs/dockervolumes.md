# Docker Volumes
In this tutorial, we will explore Docker volumes, a powerful feature that enables data persistence and sharing between Docker containers and the host system. 

## What is a Docker Volume? 

- A Docker Volume is a way to store and manage data separately from a Docker container. 
- A *[Docker Volume](https://docs.docker.com/storage/volumes/)* allows data to persist even after the containers are stopped or removed. 
- It provides durable and flexible storage solution for sharing data between containers and the host system. 
- Volumes are useful for preserving critical data like databases or application files, making it easier to manage and backup data in Docker environments.
- The another mechanism to persist data is [Bind Mounts](https://docs.docker.com/storage/bind-mounts/).

### Why to use Docker Volumes?

Docker volumes offer several benefits that significantly improve how we handle data within Docker containers. Some of the benefits are listed below: 

- **Data Persistence:** Keeping your data accessible and available for the duration of a container's life is one of the best features of Docker volumes. Your crucial data, such as databases or crucial application files, is thus safe even if you stop or remove the container. 

- **Data Sharing:** Volumes make it simple to share data between many containers. This is especially useful in configurations such as microservices architectures, where different portions of your app must communicate and share information. 

- **Backup and restore:** When it comes to backing up and restoring your data, Docker volumes come in handy. As your data is stored in a volume outside the container, you may quickly backup its contents from the host machine. This ensures the safety of your data, and if any unfortunate system failures or unanticipated issues emerge, you can immediately restore your vital data without much difficulty, minimizing downtime and preventing data loss.

- **Versioning and Upgrades:**  Docker volumes enable you separate your data from the containers, allowing you to update or replace containers without affecting your data. Your data remains consistent and compatible with later container versions, making upgrades quick and easy without the risk of data corruption or loss.

## Types of Docker Volumes

Docker provides various types of volumes, each with its own set of use cases and characteristics. Let's look at the three primary forms of Docker volumes:

### Named Volumes
 A named volume in Docker is a user-defined volume with a specific name that provides a convenient way to persist and manage data separately from the containers. When you create them, you give them a unique name, and Docker manages their lifecycle. These volumes survive even after the related containers are removed, making them excellent for long-term data storage.

***How to create a named volume?***

You can create a named volume in Docker using the ```docker volume create``` command. 

**Example**
```bash
docker volume create my_named_volume
```

Lets confirm that the volume has been created. To list all the volumes that have been created on a Docker host, we can use the following command

```bash
docker volume ls
```

Let's create a container to persist MySQL database data and mount the named volume we just created. MySQL stores its database files in the ```/var/lib/mysql``` directory. 

```bash
docker run -d --name mysql_db -e MYSQL_ROOT_PASSWORD=12345678 -v my_named_volume:/var/lib/mysql mysql
```

**In this command:**

- `-d`: Runs the container in the background (detached mode).
- `--name mysql_db`: Specifies a custom name for the container (you can choose any name).
- `-e MYSQL_ROOT_PASSWORD=12345678`: Sets the root password for the MySQL database.
- `-v my_named_volume:/var/lib/mysql`: Mounts the named volume `mysql_data` to the `/var/lib/mysql` directory inside the container.
- `mysql`: Specifies the latest version of the MySQL image.


**Accessing MySQL Database**

Now that the MySQL container is running and is using the named volume, we can access the MySQL database 

```bash
# Connect to the MySQL database
docker exec -it mysql_db mysql -u root -p

# Lets create a couple of databases
create database db1; 
create database db2;

#lets check our databases
show databases; 

# To exit the MySQL 
exit;

```

> The named volume my_named_volume will store the MySQL data on the Docker host, inside the Docker volumes directory. This allows you to maintain the database data even if you stop or remove the MySQL container. 

Let's now delete the container and create a new one while using the same volume. This will help us verify if the data still exists within the volume after recreating the container.

```bash
# To force remove a Docker container
docker rm -f mysql_db

# To create a container and mount the volume that we created earlier. 
docker run -d --name mysql_db1 -e MYSQL_ROOT_PASSWORD=12345678 -v my_named_volume:/var/lib/mysql mysql

# Connect to the MySQL database
docker exec -it mysql_db1 mysql -u root -p

#lets check our databases
show databases; 

# you may exit the MySQL 
exit;
```

The databases we created earlier persist and remain available even after creating a new container. This demonstrates the power of using Docker volumes to ensure data persistence and accessibility across container lifecycle.

***Where is the named volume located?***

Named volumes are stored within the Docker host's filesystem. By default, Docker stores named volumes in the **/var/lib/docker/volumes** directory on Linux systems.
> However, the exact location may vary depending on your Docker installation and configuration.

To inspect a named volume and view its details, including the location on the host system, you can use the `docker volume inspect` command. Here's an example:

```bash
docker volume inspect my_named_volume
```
Replace ```my_named_volume``` with the name of the named volume you want to inspect.

The output will provide a ```JSON representation``` of the named volume's properties, including its Name, Driver, Mountpoint, and other relevant information. The **"Mountpoint"** field in the output will indicate the **location** on the host system where the named volume is stored.

For example:
```json
    {
        "CreatedAt": "2023-07-19T12:34:56Z",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/my_named_volume/_data",
        "Name": "my_named_volume",
        "Options": {},
        "Scope": "local"
    }
```
In this example, the "Mountpoint" field shows that the named volume my_named_volume is located at /var/lib/docker/volumes/my_named_volume/_data on the host system. This is where the data for the named volume is stored.

**On Windows, Docker volumes are located at the following path**:
`\\wsl.localhost\docker-desktop-data\data\docker\volumes` or 

`\\wsl.localhost\docker-desktop\mnt\docker-desktop-disk\data\docker\volumes`


### Docker Bind Mounts

Bind mounts are directories or files mounted from the host system inside a Docker container. Any changes performed in either the container or the host system are immediately reflected in both places when using bind mounts. This allows for smooth file and data access between the host and the container.

***How to use Bind Mounts?***

In order to use a bind mount, When running a container, we must specify both the destination directory inside the container and the source directory or file on the host.  

**Example**

```bash
docker run -d --name my_container -v /host/path:/container/path my_image
```
**In this command:**

- `-d`: Runs the container in the background (detached mode).
- `--name my_container`: Specifies a custom name for the container.
- `-v /host/path:/container/path`: Mounts the directory at /host/path on the host to /container/path inside the container.
- `my_image`: Specifies the Docker image to use for the container.

***Benefits of Bind Mounts***

Bind mounts offer several advantages:

- **Direct Access**: Bind mounts provide direct access to host files and directories from within the container, allowing seamless data sharing and modification.

- **Host Data Persistence**: Data stored in a bind mount remains intact even if the container is removed, making it ideal for persisting critical data.

- **Development Workflow**: Bind mounts are extremely useful during development as they enable real-time changes and updates without the need to rebuild the Docker image.

***Practical Use Case of Bind Mount***

Let's illustrate a practical use case of using a bind mount. Assume we have a web application code on the host system, and we want to test it inside a Docker container without rebuilding the image each time we make changes.

```bash
# Assuming our web app code is in /path/to/my_web_app
docker run -d -v "$(pwd)/web_app:/usr/share/nginx/html" -p 8084:80 nginx

```

In this example, we're running an Nginx web server container (nginx image) and mounting our local web app code directory (/path/to/my_web_app) into the container at /usr/share/nginx/html. Any changes made to the web app on the host will immediately reflect inside the container, simplifying the development and testing process.

### Anonymous Volumes
When a container is started without specifying a specific named volume or bind mount, Docker creates volumes that are associated to the container. These volumes are known as anonymous volumes. 

***How to use Anonymous Volumes?***

When running a container, just omit the -v or --volume option to utilize an anonymous volume. Docker will then create the volume for you.

**Example**

```bash
docker run -d --name my_container my_image
```
- `-d`: Runs the container in the background (detached mode).
- `--name my_container`: Specifies a custom name for the container.
- `my_image`: Specifies the Docker image to use for the container.

***Where is the anonymous volume located?***

Anonymous volumes in Docker are located within the Docker data directory on the host system. The exact location of the Docker data directory depends on the operating system and the Docker installation method.

For most standard Docker installations:

- On Linux: Anonymous volumes are stored in the `/var/lib/docker/volumes` directory.

- On Windows: Anonymous volumes are stored in the `C:\ProgramData\docker\volumes` directory.

- On macOS: Anonymous volumes are stored in the `/var/lib/docker/volumes` directory (running Docker in a Linux VM).

It's important to note that anonymous volumes are managed by Docker and are assigned a random, unique name. They are not explicitly named like named volumes. Docker takes care of the volume creation, naming, and cleanup when the container is removed.

To inspect the anonymous volume location, you can use the following command:

```bash
docker volume inspect <volume_name>
```

***Benefits of Anonymous Volumes***

Anonymous volumes offer several advantages:

- **Simplified Setup**: Anonymous volumes eliminate the need to pre-create volumes, making it easier and quicker to start containers without worrying about volume management.

- **Automatic Cleanup**: When the container is removed, any anonymous volumes associated with it are automatically removed as well, reducing the risk of unused volumes cluttering your system.


## List of Docker Volume Commands
1. **Create a Named Volume**
   ```bash
   docker volume create my_volume
    ```
2. **List Docker Volumes**
   ```bash
   docker volume ls
   ```
3. **Inspect a Volume**
    ```bash
    docker volume inspect my_volume
    ```
4. **Remove a Volume**
    ```bash
    docker volume rm my_volume
    ```
5. **Create and Run a Container with a Named Volume**
   ```bash 
    docker run -d --name my_container -v my_volume:/path/in/container my_image
    ```
    > This command creates a container with a named volume my_volume attached to the container's /path/in/container directory.

6. **Bind Mount a Host Directory to a Container**
    ```bash
     docker run -d --name my_container -v /host/path:/container/path my_image
    ```
    > This command binds the host directory /host/path to the container's /container/path directory.

7. **Use Anonymous Volumes**
    ```bash
    docker run -d --name my_container my_image
    ```
     > In this case, an anonymous volume will be automatically created and attached to the container.

8. **To remove (pruning) all unused volumes** 
    ```bash
    docker volume prune
    ```
9. **To start a container with a volume**

When you start a container with a volume that does not exist, the Docker will create the volume for you. 
***Lets start alpine with a volume to which we will add persistent data.***

- Lets create a new volume called alpinedata
```docker
docker volume create alpinedata
```
- Create a Docker container & attach the alpinedata volume
```docker
docker run -it -v alpinedata:/data alpine /bin/sh
```
- Lets get to the data directory in container and create some files and ensure with ls command that the files are created.
```bash
touch data/file1.txt data/file2.txt
ls data
```
- Exit the container `exit`
- Delete the container `docker rm continaerid`

10. **Run a new container and attach the alpinedata volume to it.** 
```bash
docker run -it -v alpinedata:/data alpine /bin/sh
```
***[Read More on Volumes](https://docs.docker.com/storage/volumes/)***

## Exercise 
1. **Task 1: Create a Named Volume and Run a MySQL Database**

   Create a named volume with your first name and last name as "fname_lname" and then use this volume to run a MySQL database container.

2. **Task 2: Create a Bind Mount and Run a MySQL Database**

   Create a bind mount called "mysqlData" and use it to run a MySQL database container. Bind the mount to a specific directory inside the container.

3. **Task 3: Locate the Named Volume on Your Host Machine**

   After creating the named volume "fname_lname," find and type the absolute path on your host machine where this volume is located.

4. **Task 4: Run an Image Using Anonymous Volume**

   Run any Docker image of your choice and utilize an anonymous volume. Locate and identify the directory on your host system where this anonymous volume is created.



