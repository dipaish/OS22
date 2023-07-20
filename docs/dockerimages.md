##  Building Docker Images with Dockerfile

Previously, we utilized **images** from the **registry (Dockerhub)** and instructed the Docker client to run a container based on that **image**. In this section, we will learn to create custom Docker images using Dockerfile. 

### What is an image?
***Docker images*** are like blueprints that contain the application code, runtime environment, libraries, dependencies, and other configuration required to run an application. They are essential for containerization. Docker images are lightweight, portable, and allow for consistent deployments across different environments.

 **Let's begin by listing the local images available on the device and understand important concepts about images.**
```bash
$ docker images
```
**Example**
```
$ docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
alpine        latest    9c6f07244728   2 weeks ago     5.54MB
hello-world   latest    feb5d9fea6a5   11 months ago   13.3kB
```
Above is a list of **images** that I've pulled from the registry. You will have a different list of images on your machine. 

- The `TAG` refers to a particular snapshot of the image and the `ID` is the corresponding unique identifier for that image.

**Images** can be [committed](https://docs.docker.com/engine/reference/commandline/commit/) with changes and have multiple versions. 

>When you do not provide a specific version number, the client defaults to `latest`.

For example you could pull a specific version of `ubuntu` image as follows:

```bash
$ docker pull ubuntu:20.04
```

- If you do not specify the version number of the image then, as mentioned, the Docker client will default to a version named `latest`.

***For example to get the latest version of ubuntu image:***

```bash
$ docker pull ubuntu
```
***Some Tips on Docker Images***
- You can get a new Docker image  from a registry (such as the **Docker hub**) or create your own. 
- There are hundreds of thousands of images available on [Docker Store](https://store.docker.com). 
- You can also **search for images** directly from the command line using `docker search`.<br> <br>
 ***For example, if you want to search a MySQL related Image*** 
```
$ docker search mysql
NAME                            DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql                           MySQL is a widely used, open-source relation…   13077     [OK]
mariadb                         MariaDB Server is a high performing open sou…   5001      [OK]
phpmyadmin                      phpMyAdmin - A web interface for MySQL and M…   614       [OK]       
percona                         Percona Server is a fork of the MySQL relati…   584       [OK]
```   
### Types of Images

- **Base images** are images that have no parent images, usually images with an `OS` like ubuntu, alpine or debian.

- **Child images** are images that are build on base images and add additional functionality.

Another key concept is the idea of _official images_ and _user images_. (Both of which can be base images or child images.)

**Official images**
- Images that are verified by Docker.
- A dedicated team is responsible for reviewing and publishing all Official Repositories content. 
- ***Examples:*** `python`, `node`, `alpine` and `nginx` images are official (base) images. To find out more about them, check out the [Official Images Documentation](https://docs.docker.com/docker-hub/official_repos/).

**User images** 
- Images created and shared by users like you and me. 
- They build on base images and add additional functionality. 
- Typically these are formatted as `user/image-name`. The `user` value in the image name is your Dockerhub user or organization name.

## How to build your own image?

***To build your own image***, you will need to write a file called **Dockerfile.** A **Dockerfile** is a simple text file that contains a set of instructions for building a Docker image. It includes step by step instructions on how an image should be built. It defines the **base image** to start from, the application code to include,the dependencies to install and the configuration settings required for the image. 
### What is a Dockerfile?
A **Dockerfile** is simply a text file that contains the build instructions for an image. It automates the image creation process.
  - It specifies a base image.
  - It includes instructions to install required tools for your app. 
  - It includes instructions to install libraries, dependencies and packages. 
  - It then builds your app. 
  - It is a step by step set of standard instructions such as `FROM`, `COPY`, `RUN`, `ENV`, `EXPOSE`, `CMD` 
  - The commands you write in a Dockerfile are almost identical to their equivalent Linux commands.
  - The ***name of the file is***  `Dockerfile` without any extension and the letter **D** is capital. 
  - Write only the minimum set of steps that is needed for your app. Avoid unnecessary components. 
### Basic Dockerfile commands
***FROM***
 - The **FROM** command must be the first line in the Dockerfile. Since images are made up of layers, you can utilize one of the official images as the foundation for your own. The **FROM** command defines your **base layer**. 

 Example:
```  
FROM alpine:3.14 
``` 
> The above line specifies that the base image is going to be alpine:3.14. 

- It accepts the image's name as parameters. You can optionally include the image version and the maintainer's Docker Hub username in the following format:`username/imagename:version`.

***WORKDIR***
- It defines the working directory of a Docker Container.
- Any RUN, CMD, COPY or ENTRYPOINT COMMAND will be executed in the specified working directory.

Example:
``` 
WORKDIR /usr/src/app/ 
``` 
> The above line sets the working directory to /usr/src/app/within the container.

***COPY***
- It copies local files or directories from the host machine into the container.
Example:
 COPY `<src>` `<destination>`

 ```  
  COPY hello.sh . 
 ``` 
> The above line copies the file hello.sh from the current working directory on the host machine into  the current working directory inside the container, in this case: /usr/src/app/

***RUN***
- It allows you to install your applications and packages you need for your app. 
- For each RUN command, Docker will run the command then create a new layer of the image. 
- The Docker daemon runs instructions in the Docker file one by one. 
- Before running the instructions, the Docker Daemon validates the file and if the syntax is incorrect, it returns an error. 
- Each run instruction is independent and it causes a new image to be created.

Example

``` 
RUN apk update
RUN apk add nano 
``` 
> The first line ```RUN apk update```  will update the package index of the package manager (apk) within the container and fetches the latest package information from the Alpine package repositories. The second line ```RUN apk add nano``` will install the nano text editor inside the container. 

***Expose***
- It tells Docker that the container listens on the specified network ports at runtime.
Example
``` 
EXPOSE 8000
``` 
> The above line exposes port 8000 to the host and allows incoming connections to the container.

***CMD***
- It defines the commands that will run when the container starts.
- Unlike a RUN, this does not create a new layer for the Image, but simply runs the command. 
- There can only be one CMD per a Dockerfile/Image. 
- If you need to run multiple commands, the best way to do that is to have the CMD run a script. 
- CMD requires that you tell it where to run the command. 
Example 
``` 
CMD ["python", "app.py"]
``` 
> The above line specifies the command ```python app.py``` to be executed when the container starts. This will run the python code written in the file app.py.

These basic Dockerfile command will allow you to create a simple Docker image containing  your application and its dependencies. As you progress with Docker, you'll encounter more advanced commands and options that can be used to fine-tune and optimize your Docker images for different use cases.

## Create your first image

Now that you have a better understanding of images and Dockerfile, it's time to create our own. Lets build a simple image using `Alpine linux` as the `base image` that includes the `nano` text editor, `copies` the script named `hello.sh` into the image and sets it to `execute` when a container is started from the image. 

**Step 1: Set up your Project Directory**
You will state by Creating a new directory for your Docker project and navigate to it in your terminal or command prompt.

```bash
$ mkdir my_docker_project
cd my_docker_project
```
**Step 2: Create your application code**
You can create a simple application in your favorite programming language. For this illustration, we'll **write a simple bash script**.Lets create a file called hello.sh with the following content.

```bash
#!/bin/sh
 echo "Hello, World, I am learning to write a Dockerfile!"
```

**Step 3:Create the Docker File**
Now, create a Dockerfile in the same project directory. The name of the file is Dockerfile without any extension. The content of the Dockerfile is as below:

> ***Note: Please use IDE such as Visual Studio code to create Dockerfile. If you use notepad, it adds .txt extension to the file and the build won't work.***

```  
# Start from the alpine image. It sets  the base image for the Docker image to the 
# Alpine Linux version 3.14. It means the new image will be based on the Alpine Linux distribution
FROM alpine:3.14 

# It defines the working directory inside the container to /usr/src/app/. 
# It is the directory where subsequent commands will be executed.
WORKDIR /usr/src/app/  

# It copies the script file hello.sh from the build context (the directory containing the Dockerfile) 
# into the /usr/src/app/ directory inside the container. 
# The dot (.) represents the current working directory inside the container, /usr/src/app/
COPY hello.sh .

#  It updates the package index of the package manager (apk) within the container.
RUN apk update

# It installs the nano text editor inside the container.
RUN apk add nano 

# It adds executable permission to the script hello.sh inside the container. 
# It allows the script to be executed as a command.
RUN chmod +x hello.sh 

# It sets the default command to be executed when a container starts from the built image. In this case, it runs the hello.sh script.

CMD ./hello.sh
``` 

**Step 4:Build the Docker Image**
### Build your first image
Now that our docker file is ready, we will use ***[docker build](https://docs.docker.com/engine/reference/commandline/build/)*** command to build our image. 

The following **docker build** command will look for the `Dockerfile` and build as per instructions in the Dockerfile. The (.) specifies where to build and -t  flag tags the image with the name "my_first_image".

```bash 
docker build -t my_first_image .
``` 

**Step 5:Run the Docker Container** 

Once the image is successfully built, you can run a container using the following command: 
```bash
docker run my-first-image
``` 
**Step 6: Access the interactive terminal and use nano to create a text file called new.txt**

```bash
docker run -it my-first-image /bin/sh
nano new.txt
```
***Congratulations! You have successfully created your first Docker image and deployed it as a container.***

***Read More at [Docker File Reference Page](https://docs.docker.com/engine/reference/builder/)***

***Lets check our image and note the size of the image (my-first-image). It is 15.8MB*** 
```
 docker images
```

***Lets remove the nano from our Dockerfile***

```
# Start from the alpine image 
FROM alpine:3.14 

# it defines the working directory
WORKDIR /usr/src/app/  

# copies the script file to the container
COPY hello.sh .

# Installs updates &  it adds executable permission to our script hello.sh (no nano this time)
RUN apk update && \
chmod +x hello.sh 

# it runs the script at the start-up
CMD ./hello.sh
```
***Now build the image again  as my-first-image2 without nano. Since vi is already available, we don't necessarily need the nano text editor.***
```
 docker build . -t my-first-image2
 ```

***Check the size of image***

```
docker images
```
When you remove the instruction to install `nano` in the Dockerfile, the size of the resulting Docker image decreases because the image no longer includes the `nano` text editor.

Here's why the size reduction occurs:

1. **Layering in Docker**: Each instruction in a Dockerfile creates a new layer in the Docker image. Layers are stackable, and each layer represents a change or modification to the image. When you install `nano` using the `RUN apk add nano` instruction, it adds a layer to the image containing the installed package.

2. **Image Size**: Docker images are composed of multiple layers. Each layer contributes to the overall size of the image. When you remove the instruction to install `nano`, that layer is no longer present in the image, resulting in a smaller image size.

By minimizing the number of unnecessary layers and reducing the size of the Docker image, you can create more efficient and lightweight images. This practice is particularly important when building production-ready containers to optimize resource usage and improve deployment times.

## Push your first image
Now that we have created and tested our image, we can push it to [Docker Hub](https://hub.docker.com/).

***First you have to login to your Docker Hub account***
```bash
docker login
```
Enter `YOUR_USERNAME` and `password` when prompted. 
```bash
 docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: deepakkc
Password: 
Login Succeeded
```
Now all you have to do is:

```bash
docker tag  my-first-image2 deepakkc/my-first-image2
docker push deepakkc/my-first-image2

```
**Now yow you may delete your image and run it again. This time it will pull image from Docker Hub.**

```
docker rmi deepakkc/my-first-image2 -f
docker run deepakkc/my-first-image2
```
> Note: if you don't have an account, visit [Docker Hub](hhttps://hub.docker.com/) and create an account. 

### Push a new version of a Docker Image

When you update your application, fix bugs, implement security updates, or make configuration changes, you may need to push a new version of the Docker image. This ensures that your containers are running with the latest changes and improvements in your application.

#### Guidelines to Push a New Version:

1. **Update the Application**: Make necessary changes and improvements to your application code.

2. **Fix Bugs**: Address and resolve any known bugs or issues in your application.

3. **Implement Security Updates**: Apply security patches and updates to protect against vulnerabilities.

4. **Configure Changes**: Make adjustments to configurations or environment variables as needed.

#### How to Push the New Image Version:

1. **Build the Image**: Use the `docker build` command to build the updated Docker image.
```
docker build -t my_first_image:v1.1 .
```
2. **Tag the Image with a New Version**: Tag the new image version using `docker tag` with a relevant version number or tag.

```
docker tag old_image_name:old_version new_image_name:new_version
```
Replace old_image_name and old_version with the current image name and version, and set new_image_name and new_version to the desired new name and version for the image.

3. **Push to Registry**: Use `docker push` to upload the new image version to your container registry.

If you haven't logged in to the container registry where you want to push the image, use the docker login command to authenticate and then push the new image version:

```
docker login
docker push new_image_name:new_version
```
By following these steps, you can keep your Docker image up-to-date with the latest changes and improvements in your application, ensuring that your containers run with the most recent version of the code and configurations.

> ***Now that you are done with this container, stop and remove it since you won't be using it again.***

>**Note:** If you want to learn more about Dockerfiles, check out [Best practices for writing Dockerfiles](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/).

## Docker Commands for Images

1. `docker images`: List all locally available Docker images.
   - Example: `docker images`

2. `docker pull`: Pull an image from a container registry.
   - Example: `docker pull nginx`

3. `docker build`: Build a Docker image from a Dockerfile.
   - Example: `docker build -t myapp:latest .`

4. `docker push`: Push an image to a container registry.
   - Example: `docker push myusername/myapp:latest`

5. `docker rmi`: Remove one or more Docker images from the local machine.
   - Example: `docker rmi myapp:latest`

6. `docker tag`: Tag an existing image to give it a new name or version.
   - Example: `docker tag myapp:latest myapp:v1.0`

7. `docker history`: Show the history of an image, including its layers and sizes.
   - Example: `docker history myapp:latest`

8. `docker inspect`: Display detailed information about an image.
   - Example: `docker inspect myapp:latest`

9. `docker search`: Search for Docker images in a public container registry like Docker Hub.
   - Example: `docker search ubuntu`

10. `docker save`: Save one or more images to a tar archive.
    - Example: `docker save -o my_images.tar myapp:latest myotherapp:latest`

11. `docker load`: Load an image or images from a tar archive.
    - Example: `docker load -i my_images.tar`

12. `docker prune`: Remove all unused images, networks, and containers (careful with this command).
    - Example: `docker system prune`

## Exercise
1. Write a Dockerfile to build a Docker image using Ubuntu. Add MySQL database service to your image and push it to DockerHub. Tag the image as username/ubuntu-git:1.1 
2. Run your image from DockerHub, get to the interactive terminal and verify that you have installed MySQL (type `sudo mysql` in terminal, if you get the mysql prompt, it's installed. To exit, type `exit`)
3. Write a Dockerfile to create an image with CMD instruction (any). Use Alpine image as the base image.  
4. Continue working on the Dockerfile created in Task 3. Specify a working directory (WORKDIR as /opt) and use RUN to write "This is my work directory " in a text file test.txt (`echo "This is my work Directory" >test.txt`). Build Docker image as **workdir:v1** and run the image as `docker run -it wordkir:v1 ls`. Make sure that you find **test.txt**. 
5. Write a Docker file to build a Docker image that runs a simple Java app in a Docker container. 
6. Cleanup: You probably don't need any of these containers, therefore you may delete all of them with the following command

```
docker rm -f $(docker ps -aq)
```

