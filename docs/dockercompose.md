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

In this project directory, we will create a file named ```docker-compose.yml```. This file will contain the configuration for our multi-container environment. 

**Step 3: Define Services in Docker Compose**

We will define services that we need in the ```docker-compose.yml``` file. 

***Let's create a simple setup with a web server using Nginx and a database using MySQL.***

In this example, we will define two services: **webserver** and **database.** 
- The **webserver** service uses the latest Nginx image and maps the host's port 80 to the container's port 80. 
- The **database** service uses the latest MySQL image and sets environment variables for the root password, 

**Example 1: A docker compose file (docker-compose.yml)**

```yaml
version:'3'

services:
  webserver:
    image: nginx
    ports:
      - "80:80"

  database:
    image: mysql
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

**Step 6: Verify the Running Containers**

To ensure that the multi-container application is running, we can use the following command to view the list of running containers:

```bash
docker-compose ps
```
In this case, we can further verify that the Nginx server is up and running by opening our web browser and navigating to http://localhost:80. 

We can also login to MySQL using the following command: 

```bash
docker exec -it your_container_id mysql -u root -p 
```

**Example 2: A docker compose file (docker-compose.yml) that uses a Dockerfile to build a custom image**

Let's assume that we have a PHP web application with a MySQL database. We will also create a custom Docker image for Apache to serve PHP pages.

**Step 1: Set Up the Project Directory**

Create a new directory and create the following files inside it:

**docker-compose.yml**: Docker Compose file to define the services.

**Dockerfile**: Custom Dockerfile to build the Apache image.

**index.php**: A simple PHP file to test the web application. You can copy the following code and save it as ```index.php```.

```php
<?php
echo "Hello, World!";
?>

```
**Step 2: Create Dockerfile for Apache**

We'll make a ```Dockerfile``` that uses the official ```PHP Apache image``` (php:7.4-apache) as our base image and ```install the MySQL extensions``` required for PHP to connect with the MySQL database. The ```working directory``` for serving web files with Apache will then be configured to ```/var/www/html```. The index.php file is then copied from the local directory to the container's /var/www/html directory. We will open Port 80 to give access to the web server. Finally, we will use the ```apache2-foreground``` command to start the Apache server within the container. 

```docker
# Use the official PHP Apache image as the base image
FROM php:7.4-apache

# Install MySQL extensions for PHP
RUN docker-php-ext-install mysqli pdo pdo_mysql

# Set the working directory inside the container
WORKDIR /var/www/html

# Copy the application code to the container
COPY index.php .

# Expose port 80 for Apache
EXPOSE 80

# Start Apache server
CMD ["apache2-foreground"]

```
**Step 3: Create docker-compose.yml**
```yaml
version: '3'

services:
  webserver:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "80:80"
    volumes:
      - ./index.php:/var/www/html/index.php
    depends_on:
      - database

  database:
    image: mysql:latest
    environment:
      MYSQL_ROOT_PASSWORD: mysecretpassword
      MYSQL_DATABASE: mydatabase
      MYSQL_USER: myuser
      MYSQL_PASSWORD: myuserpassword
    volumes:
      - db_data:/var/lib/mysql

volumes:
  db_data:
```
In the above Docker Compose file, we have defined two services: `webserver` and `database`. The `webserver` service is built using the custom Dockerfile we created earlier. It is configured to map port 80 on the host to port 80 on the container, allowing it to serve the PHP application. Additionally, the `index.php` file is mounted as a volume inside the container, enabling live code changes without the need to rebuild the image.

On the other hand, the `database` service uses the official MySQL image and sets up environment variables for the MySQL database, such as the root password, database name, user, and user password. Moreover, to ensure data persistence, the MySQL data is stored as a named volume named `db_data`. This means that even if the container is removed, the data will still be retained, avoiding any data loss. This combination of services and configurations allows us to create a complete and functional multi-container application that includes a PHP web server and a MySQL database.

**Step 4: Run Docker Compose**

Open a terminal in the project directory and run the following command to start the services:

```
docker-compose up -d
```

**Step 5: Access the Web Application**

Open your web browser and go to http://localhost. You should see the output of index.php, which means the Apache server is running and serving PHP pages.


**Notes**

- `build:` It specifies the location of a Dockerfile. The **.** sign represents the same directory where docker-compose.yml file is located.

- `ports:` It is describing how the container's port should be mapped to the host machine.

- `volumes:` We are attaching our files/directories to the containers'. 

- `depends_on:` It tells a dependency of a service to another.

## Common Docker Compose Commands

- **docker-compose up**: Start the services defined in the Docker Compose file. 

- **docker-compose up -d**: Start the services in the background (detached mode).

- **docker-compose down**: Stop and remove all containers, networks, and volumes defined in the Docker Compose file.

- **docker-compose ps**: List the running containers of the services defined in the Docker Compose file.

- **docker-compose logs**: View the logs of the services.

- **docker-compose build**: Build or rebuild the services defined in the Docker Compose file.

- **docker-compose stop**: Stop the services defined in the Docker Compose file.

- **docker-compose start**: Start the stopped services defined in the Docker Compose file.

- **docker-compose restart**: Restart the services defined in the Docker Compose file.

- **docker-compose exec**: Execute a command inside a running container.

- **docker-compose down -v**: Stop and remove all containers, networks, and volumes defined in the Docker Compose file, including volumes.

- **docker-compose config**: Validate and view the composed configuration file.


## Exercise 
1. Follow the guidelines available at this link and get more familiar with Docker Compose by completing the task. 
  [Get Started with Docker Compose](https://docs.docker.com/compose/gettingstarted/) 

2. Complete the task available at this link [Setting up Local WordPress Development using - Docker Compose](https://dipaish.github.io/docker/index.html) 