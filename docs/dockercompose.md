# Docker Compose

In this article, we'll go over Docker Compose's fundamentals and learn how to build up a simple multi-container environment. 

## What is Docker Compose? 
Docker Compose is a powerful tool that allows you to define and manage multi-container applications using a YAML file, . Complex applications are easier to maintain since it makes operating and connecting numerous Docker containers simpler. 

- It includes A YAML (Ain't Markup Language) file to configure your application’s services. 
-  Once you have created a Compose file, you can create and start your application with a single command: `docker compose up`
- With Docker Compose:
    - You can start/stop multiple services with a single command `Docker compose up` `Docker compose down`
    - You can also use docker to scale up/down services when needed

> Note: Docker Compose V2 is a major version release of Docker Compose. It introduced several new features and improvements over the original version (v1).

## Installing Docker Compose

To get Docker Compose, the easiest and recommended way is to **install Docker Desktop.** Docker Desktop includes Docker Compose, as well as the necessary components like Docker Engine and Docker CLI, which Compose relies on.

You can install [Docker Desktop](https://www.docker.com/products/docker-desktop) on Linux, Mac, and Windows operating systems.

## How to use Docker Compose?

Using Docker Compose is basically a three-step process:
1. Define your app's environment with a `Dockerfile` so it can be reproduced anywhere.
2. Define the services that make up your app in `docker-compose.yml` file so they can be run together in an isolated environment.
3. Lastly, run `docker compose up` and Compose will start and run your entire service.

### Writing Your First Docker Compose File

When writing a Docker Compose file, it's essential to begin by considering the services your application requires, such as a web server and a database. This step is crucial as it lays the foundation for defining the different components of your multi-container setup.

For example, if your web application requires a web server to serve web pages and a database to store data, you would define two services in your Docker Compose file: one for the web server (e.g., Nginx or Apache) and another for the database (e.g., MySQL or PostgreSQL).

***Let's walk through the step-by-step process of building a multi-container service using Docker Compose.***

**Step 1: Set Up the Project Directory**
For our Docker Compose project, we will make a new directory. We'll construct the files required for our multi-container application in this directory.

```bash
mkdir my_compose_project
cd my_compose_project
```

**Step 2: Create a Docker Compose YAML File**

In this project directory, we will create a file named ```docker-compose.yml```. This file will contain the configuration for your multi-container environment. 

**Step 3: Define Services in Docker Compose**

We will define services that we need in the ```docker-compose.yml``` file. 

***Let's create a simple setup with a web server using Nginx and a database using MySQL.***

In this example, we will define two services: **webserver** and **database.** 
- The **webserver** service uses the latest Nginx image and maps the host's port 80 to the container's port 80. 
- The **database.** service uses the latest MySQL image and sets environment variables for the root password, 

A docker compose file (docker-compose.yml) looks like this

```yaml
version:'3'

services:
  webserver:
    image: nginx:latest
    ports:
      - "80:80"

  database:
    image: mysql:latest
    environment:
      MYSQL_ROOT_PASSWORD: password

```

- `version: '3'`: This specifies the version of the Docker Compose file syntax being used. In this case, it's version 3, which indicates the compatibility with specific features and configurations.

- `services`: This section defines the different services (containers) that will be part of our multi-container application.

  - `webserver`: This service uses the latest version of the Nginx image available on Docker Hub. The `ports` section maps the host's port 80 to the container's port 80, allowing access to the web server from the host machine.

  - `database`: This service uses the latest version of the MySQL image available on Docker Hub. The `environment` section sets up environment variables for the MySQL database, in this case the root password.

**Step 4: Check the validity of a Docker Compose file**

To check the validity of a Docker Compose file, we can use the ```docker-compose config``` command.This command checks the syntax and validates the Docker Compose file against the version specified and the Docker Compose file format rules. It ensures that the file is correctly written and does not contain any errors or inconsistencies. 

```bash
docker-compose config
```

**Step 5: Run the Multi-Container Application**

Now that we have defined the services in the docker-compose.yml file, it's time to run our multi-container application using Docker Compose. Open a terminal, navigate to your project directory, and execute the following command:

```bash
docker-compose up -d
```

Step 6: Verify the Running Containers

To ensure that the multi-container application is running, we can use the following command to view the list of running containers:

```bash
docker-compose ps
```
In this case, we can further verify that the Nginx server is up and running by opening our web browser and navigating to http://localhost:80. 








- `version:3`: It specifies the version of the docker-compose file syntax.
- `services:`It describes the services that are to be run. In the above example, we have two services **web** & **redis.**
- `web:` It is the name of the service. Docker Compose will create the container with the name we provide. 

- `build:` It specifies the location of a Dockerfile. The **.** sign represents the same directory where docker-compose.yml file is located.

- `redis:` Redis is the name of the service that is built from the redis image. Docker Compose will create the container for the redis service.
- `ports:` It is describing how the container's port should be mapped to the host machine.

- `volumes:` We are attaching our files/directories to the containers'. 
- `image:` To specify a pre-built image that we want to use

- `depends_on:` It tells a dependency of a service to another.

## Creating your first Docker Compose file.
1. Create a directory and get into the directroy 
```
mkdir docker99 
cd docker99
```
2. Create a docker-compose.yml file 

```yml
services:
  database:
    image: mysql
    environment:
      MYSQL_ROOT_PASSWORD: password
    ports:
      - "6034:3306"
    volumes: # it persists updates made to the database
      - dbdata:/var/lib/mysql
  phpmyadmin:
    depends_on:
      - database
    image: phpmyadmin
    ports:
      - "8000:80"
    environment:
      PMA_HOST: database
      MYSQL_ROOT_PASSWORD: password
volumes:
  dbdata:

```

3. Check the validity of the file 
```
docker-compose config
```

4. Run the docker-compose file in the detached mode

```
Docker-compose up -d
```

5. Check your images & containers and you will find new images & containers 

```
Docker images
Docker ps 
```
Get to the link in the browser: [http://localhost:8000/](http://localhost:8000/) and login with the root username and password. 

### Docker-compose Common Commands

- Starts existing containers for a service.
`docker-compose start`

- Stops running containers without removing them.
`docker-compose stop`

- Pauses running containers of a service.
`docker-compose pause`

- Unpauses paused containers of a service.¶
`docker-compose unpause`

- Lists containers.
`docker-compose ps`

- Builds, (re)creates, starts, and attaches to containers for a service.
`docker-compose up`

- Stops containers and removes containers, networks, volumes, and images created by up.
`docker-compose down`

### Exercise 
1. Follow the guidelines available at this link and get more familiar with Docker Compose by completing the task. 
  [Get Started with Docker Compose](https://docs.docker.com/compose/gettingstarted/) 

2. Complete the task available at this link [Setting up Local WordPress Development using - Docker Compose](https://dipaish.github.io/docker/index.html) 