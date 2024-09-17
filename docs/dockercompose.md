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
services:
  webserver:
    image: nginx
    ports:
      - "80:80"
  database:
    image: mysql
    environment:
      MYSQL_ROOT_PASSWORD: password
    ports:
      - "3306:3306"   
```
- `services`: This section defines the different services (containers) that will be part of our multi-container application.

  - `webserver`: This service uses the latest version of the Nginx image available on Docker Hub. The `ports` section maps the host's port 80 to the container's port 80, allowing access to the web server from the host machine.

  - `database`: This service uses the latest version of the MySQL image available on Docker Hub. The `environment` section sets up environment variables for the MySQL database, in this case the root password and the`ports` section maps the host's port 3306 to the container's port 3306. 

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
We can improve the above docker-compose.yml file with the use of separate **.env** files for passwords and other sensitive parameters that should not reside within the configuration file itself. This is done to secure the credentials and also to ease the configuration. **Create a .env file** outside your project directory and add these variables there and use it in the docker- compose.yml for keeping it safe as well as flexible. 
```bash
# Define the services that make up your application
services:
  # Web server service using the latest Nginx image
  webserver:
    image: nginx:latest  # Use the latest stable version of Nginx
    ports:
      - "80:80"  # Map port 80 on the host to port 80 on the container
    restart: always  # Always restart the container if it stops
    networks:
      - webnet  # Connect to the 'webnet' network

  # Database service using MySQL 8.0
  database:
    image: mysql:8.0  # Use MySQL version 8.0
    environment:
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}  # Root password from environment variable
      MYSQL_DATABASE: mydatabase  # Name of the default database
      MYSQL_USER: user  # Username for the database
      MYSQL_PASSWORD: ${MYSQL_PASSWORD}  # User password from environment variable
    ports:
      - "3306:3306"  # Map port 3306 on the host to port 3306 on the container
    restart: always  # Always restart the container if it stops
    networks:
      - webnet  # Connect to the 'webnet' network

# Define the network used by the services
networks:
  webnet:  # Create a custom network named 'webnet'

```
***.env file***
```bash
# .env file
MYSQL_ROOT_PASSWORD=your_root_password
MYSQL_DATABASE=mydatabase
MYSQL_USER=user
MYSQL_PASSWORD=your_user_password
```

**Step 6: Verify the Running Containers**

To ensure that the multi-container application is running, we can use the following command to view the list of running containers:

```bash
docker-compose ps
```
In this case, we can further verify that the Nginx server is up and running by opening our web browser and navigating to http://localhost:80. 

We can also login to MySQL using the following command: or you can use tools such as phpMyadmin or MySQL Workbench. 

```bash
docker exec -it your_container_id mysql -u root -p 
```

**Example 2: A docker compose file (docker-compose.yml) that uses a Dockerfile to build a custom image**

Let's assume that we have a PHP web application with a MySQL database. We will also create a custom Docker image for Apache to serve PHP pages.

**Step 1: Set Up the Project Directory**

Create a new directory and create the following files inside it:

**docker-compose.yml**: Docker Compose file to define the services.

**Dockerfile**: Custom Dockerfile to build the Apache image.

**index.php**: A simple PHP file to test the web application. You can create a folder named **public**, copy the following code and save it as ```index.php```.

```php
<?php
echo "Hello, World!";
?>

```
**Step 2: Create Dockerfile for Apache**

We'll make a ```Dockerfile``` that uses the official ```PHP Apache image``` (php:7.4-apache) as our base image and ```install the MySQL extensions``` required for PHP to connect with the MySQL database. The ```working directory``` for serving web files with Apache will then be configured to ```/var/www/html```. The index.php file is then copied from the local directory to the container's /var/www/html directory. We will open Port 80 to give access to the web server. Finally, we will use the ```apache2-foreground``` command to start the Apache server within the container. 

```bash
# Use the official PHP Apache image as the base image
FROM php:7.4-apache

# Install PHP MySQL extensions
RUN docker-php-ext-install mysqli pdo pdo_mysql && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# Set the working directory inside the container
WORKDIR /var/www/html

# Copy the 'public' folder from the host to the container
COPY public/ .

# Ensure the appropriate file permissions for Apache (non-root)
RUN chown -R www-data:www-data /var/www/html && \
    chmod -R 755 /var/www/html

# Expose port 80 for Apache
EXPOSE 80

# Start Apache server as the main process
CMD ["apache2-foreground"]

```
**Step 3: Create docker-compose.yml**
```yaml
services:
  webserver:
    build:
      context: .  # The directory containing the Dockerfile
      dockerfile: Dockerfile  # Dockerfile to use for building the web server
    ports:
      - "80:80"  # Map port 80 on the host to port 80 in the container, first par is host port on the host machine while second part is the port inside container
    volumes:
      - ./public:/var/www/html/index.php  # Bind-mount local index.php
    depends_on:
      - database  # Ensure the database service starts before the webserver

  database:
    image: mysql:latest  # Use the latest MySQL image from Docker Hub
    environment:
      MYSQL_ROOT_PASSWORD: mysecretpassword  # Set the root password for MySQL
      MYSQL_DATABASE: mydatabase  # Create a default database
      MYSQL_USER: myuser  # Create a user for the database
      MYSQL_PASSWORD: myuserpassword  # Set the password for the created user
    volumes:
      - db_data:/var/lib/mysql  # Persist MySQL data using a named volume

volumes:
  db_data:  # Define the named volume to persist database data

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

**Task 1: Getting Started with Docker Compose**

To begin your journey with Docker Compose, visit the page [Get Started with Docker Compose](https://docs.docker.com/compose/gettingstarted/) and follow the step by step guidelines. This guide will introduce you to the basics of Docker Compose and walk you through the process of defining and running multi-container applications using a simple example.


**Task 2: Practice with a Task**

Next, put your newly acquired knowledge into practice by completing the task available at [Setting up Local WordPress Development using - Docker Compose](https://dipaish.github.io/docker/index.html) This task will guide you in setting up a local WordPress development environment using Docker Compose. You will learn how to configure and run a WordPress container along with a MySQL database, providing you with hands-on experience with Docker Compose for real-world scenarios.