# 2. Docker Images

Previous, we utilized **images** from the **registry**  and asked the Docker client to run a container **based** on that image. 

To see the list of images that are available locally on your system, run the `docker images` command.

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

### Create your first image
Now that you have a better understanding of images, it's time to create your own.

#### Docker File
- A Dockerfile is simply a text file that contains the build instructions for an image. It automates the image creation process.
- The name of the file is  `Dockerfile` without any extension and the letter **D** is capital. 

### Example of a docker file 

### Build the image

### Run your image

### Push your image
 

**The goal of this exercise is to create a Docker image which will run a Flask app.**

We'll do this by first pulling together the components for a random cat picture generator built with Python Flask, then _dockerizing_ it by writing a _Dockerfile_. Finally, we'll build the image, and then run it.

- [Create a Python Flask app that displays random cat pix](#231-create-a-python-flask-app-that-displays-random-cat-pix)
- [Write a Dockerfile](#232-write-a-dockerfile)
- [Build the image](#233-build-the-image)
- [Run your image](#234-run-your-image)
- [Dockerfile commands summary](#235-dockerfile-commands-summary)

### 2.3.1 Create a Python Flask app that displays random cat pix

For the purposes of this workshop, we've created a fun little Python Flask app that displays a random cat `.gif` every time it is loaded - because, you know, who doesn't like cats?

Start by creating a directory called ```flask-app``` where we'll create the following files:

- [app.py](#apppy)
- [requirements.txt](#requirementstxt)
- [templates/index.html](#templatesindexhtml)
- [Dockerfile](#dockerfile)

Make sure to ```cd flask-app``` before you start creating the files, because you don't want to start adding a whole bunch of other random files to your image.

#### app.py

Create the **app.py** with the following content:

```python
from flask import Flask, render_template
import random

app = Flask(__name__)

# list of cat images
images = [
   "http://img.buzzfeed.com/buzzfeed-static/static/2013-10/enhanced/webdr05/15/9/anigif_enhanced-buzz-26388-1381844103-11.gif",
    "http://img.buzzfeed.com/buzzfeed-static/static/2013-10/enhanced/webdr01/15/9/anigif_enhanced-buzz-31540-1381844535-8.gif",
    "http://img.buzzfeed.com/buzzfeed-static/static/2013-10/enhanced/webdr05/15/9/anigif_enhanced-buzz-26390-1381844163-18.gif",
    "http://img.buzzfeed.com/buzzfeed-static/static/2013-10/enhanced/webdr06/15/10/anigif_enhanced-buzz-1376-1381846217-0.gif",
    "http://img.buzzfeed.com/buzzfeed-static/static/2013-10/enhanced/webdr03/15/9/anigif_enhanced-buzz-3391-1381844336-26.gif",
    "http://img.buzzfeed.com/buzzfeed-static/static/2013-10/enhanced/webdr06/15/10/anigif_enhanced-buzz-29111-1381845968-0.gif",
    "http://img.buzzfeed.com/buzzfeed-static/static/2013-10/enhanced/webdr03/15/9/anigif_enhanced-buzz-3409-1381844582-13.gif",
    "http://img.buzzfeed.com/buzzfeed-static/static/2013-10/enhanced/webdr02/15/9/anigif_enhanced-buzz-19667-1381844937-10.gif",
    "http://img.buzzfeed.com/buzzfeed-static/static/2013-10/enhanced/webdr05/15/9/anigif_enhanced-buzz-26358-1381845043-13.gif",
    "http://img.buzzfeed.com/buzzfeed-static/static/2013-10/enhanced/webdr06/15/9/anigif_enhanced-buzz-18774-1381844645-6.gif",
    "http://img.buzzfeed.com/buzzfeed-static/static/2013-10/enhanced/webdr06/15/9/anigif_enhanced-buzz-25158-1381844793-0.gif",
    "http://img.buzzfeed.com/buzzfeed-static/static/2013-10/enhanced/webdr03/15/10/anigif_enhanced-buzz-11980-1381846269-1.gif"
    ]

@app.route('/')
def index():
    url = random.choice(images)
    return render_template('index.html', url=url)

if __name__ == "__main__":
    app.run(host="0.0.0.0")
```

#### requirements.txt

In order to install the Python modules required for our app, we need to create a file called **requirements.txt** and add the following line to that file:

```
Flask==0.10.1
```

#### templates/index.html

Create a directory called `templates` and create an **index.html** file in that directory with the following content in it:

```html
<html>
  <head>
    <style type="text/css">
      body {
        background: black;
        color: white;
      }
      div.container {
        max-width: 500px;
        margin: 100px auto;
        border: 20px solid white;
        padding: 10px;
        text-align: center;
      }
      h4 {
        text-transform: uppercase;
      }
    </style>
  </head>
  <body>
    <div class="container">
      <h4>Cat Gif of the day</h4>
      <img src="{{url}}" />
      <p><small>Courtesy: <a href="http://www.buzzfeed.com/copyranter/the-best-cat-gif-post-in-the-history-of-cat-gifs">Buzzfeed</a></small></p>
    </div>
  </body>
</html>
```

### 2.3.2 Write a Dockerfile

We want to create a Docker image with this web app. As mentioned above, all user images are based on a _base image_. Since our application is written in Python, we will build our own Python image based on [Alpine](https://store.docker.com/images/alpine). We'll do that using a **Dockerfile**.

A [Dockerfile](https://docs.docker.com/engine/reference/builder/) is a text file that contains a list of commands that the Docker daemon calls while creating an image. The Dockerfile contains all the information that Docker needs to know to run the app &#8212; a base Docker image to run from, location of your project code, any dependencies it has, and what commands to run at start-up. It is a simple way to automate the image creation process. The best part is that the [commands](https://docs.docker.com/engine/reference/builder/) you write in a Dockerfile are *almost* identical to their equivalent Linux commands. This means you don't really have to learn new syntax to create your own Dockerfiles.


1. Create a file called **Dockerfile**, and add content to it as described below.

  We'll start by specifying our base image, using the `FROM` keyword:

  ```
  FROM alpine:3.5
  ```

2. The next step usually is to write the commands of copying the files and installing the dependencies. But first we will install the Python pip package to the alpine linux distribution. This will not just install the pip package but any other dependencies too, which includes the python interpreter. Add the following [RUN](https://docs.docker.com/engine/reference/builder/#run) command next:

  ```
  RUN apk add --update py2-pip
  ```

3. Let's add the files that make up the Flask Application.

  Install all Python requirements for our app to run. This will be accomplished by adding the lines:

  ```
  COPY requirements.txt /usr/src/app/
  RUN pip install --no-cache-dir -r /usr/src/app/requirements.txt
  ```

  Copy the files you have created earlier into our image by using [COPY](https://docs.docker.com/engine/reference/builder/#copy)  command.

  ```
  COPY app.py /usr/src/app/
  COPY templates/index.html /usr/src/app/templates/
  ```

4. Specify the port number which needs to be exposed. Since our flask app is running on `5000` that's what we'll expose.

  ```
  EXPOSE 5000
  ```

5. The last step is the command for running the application which is simply - `python ./app.py`. Use the [CMD](https://docs.docker.com/engine/reference/builder/#cmd) command to do that:

  ```
  CMD ["python", "/usr/src/app/app.py"]
  ```

  The primary purpose of `CMD` is to tell the container which command it should run by default when it is started.

6. Verify your Dockerfile.

  Our `Dockerfile` is now ready. This is how it looks:

  ```
  # our base image
  FROM alpine:3.5

  # Install python and pip
  RUN apk add --update py2-pip

  # install Python modules needed by the Python app
  COPY requirements.txt /usr/src/app/
  RUN pip install --no-cache-dir -r /usr/src/app/requirements.txt

  # copy files required for the app to run
  COPY app.py /usr/src/app/
  COPY templates/index.html /usr/src/app/templates/

  # tell the port number the container should expose
  EXPOSE 5000

  # run the application
  CMD ["python", "/usr/src/app/app.py"]
  ```

### 2.3.3 Build the image

Now that you have your `Dockerfile`, you can build your image. The `docker build` command does the heavy-lifting of creating a docker image from a `Dockerfile`.

When you run the `docker build` command given below, make sure to replace `<YOUR_USERNAME>` with your username. This username should be the same one you created when registering on [Docker Cloud](https://cloud.docker.com). If you haven't done that yet, please go ahead and create an account.

The `docker build` command is quite simple - it takes an optional tag name with the `-t` flag, and the location of the directory containing the `Dockerfile` - the `.` indicates the current directory:

```
$ docker build -t <YOUR_USERNAME>/myfirstapp .
Sending build context to Docker daemon 9.728 kB
Step 1 : FROM alpine:latest
 ---> 0d81fc72e790
Step 2 : RUN apk add --update py-pip
 ---> Running in 8abd4091b5f5
fetch http://dl-4.alpinelinux.org/alpine/v3.3/main/x86_64/APKINDEX.tar.gz
fetch http://dl-4.alpinelinux.org/alpine/v3.3/community/x86_64/APKINDEX.tar.gz
(1/12) Installing libbz2 (1.0.6-r4)
(2/12) Installing expat (2.1.0-r2)
(3/12) Installing libffi (3.2.1-r2)
(4/12) Installing gdbm (1.11-r1)
(5/12) Installing ncurses-terminfo-base (6.0-r6)
(6/12) Installing ncurses-terminfo (6.0-r6)
(7/12) Installing ncurses-libs (6.0-r6)
(8/12) Installing readline (6.3.008-r4)
(9/12) Installing sqlite-libs (3.9.2-r0)
(10/12) Installing python (2.7.11-r3)
(11/12) Installing py-setuptools (18.8-r0)
(12/12) Installing py-pip (7.1.2-r0)
Executing busybox-1.24.1-r7.trigger
OK: 59 MiB in 23 packages
 ---> 976a232ac4ad
Removing intermediate container 8abd4091b5f5
Step 3 : COPY requirements.txt /usr/src/app/
 ---> 65b4be05340c
Removing intermediate container 29ef53b58e0f
Step 4 : RUN pip install --no-cache-dir -r /usr/src/app/requirements.txt
 ---> Running in a1f26ded28e7
Collecting Flask==0.10.1 (from -r /usr/src/app/requirements.txt (line 1))
  Downloading Flask-0.10.1.tar.gz (544kB)
Collecting Werkzeug>=0.7 (from Flask==0.10.1->-r /usr/src/app/requirements.txt (line 1))
  Downloading Werkzeug-0.11.4-py2.py3-none-any.whl (305kB)
Collecting Jinja2>=2.4 (from Flask==0.10.1->-r /usr/src/app/requirements.txt (line 1))
  Downloading Jinja2-2.8-py2.py3-none-any.whl (263kB)
Collecting itsdangerous>=0.21 (from Flask==0.10.1->-r /usr/src/app/requirements.txt (line 1))
  Downloading itsdangerous-0.24.tar.gz (46kB)
Collecting MarkupSafe (from Jinja2>=2.4->Flask==0.10.1->-r /usr/src/app/requirements.txt (line 1))
  Downloading MarkupSafe-0.23.tar.gz
Installing collected packages: Werkzeug, MarkupSafe, Jinja2, itsdangerous, Flask
  Running setup.py install for MarkupSafe
  Running setup.py install for itsdangerous
  Running setup.py install for Flask
Successfully installed Flask-0.10.1 Jinja2-2.8 MarkupSafe-0.23 Werkzeug-0.11.4 itsdangerous-0.24
You are using pip version 7.1.2, however version 8.1.1 is available.
You should consider upgrading via the 'pip install --upgrade pip' command.
 ---> 8de73b0730c2
Removing intermediate container a1f26ded28e7
Step 5 : COPY app.py /usr/src/app/
 ---> 6a3436fca83e
Removing intermediate container d51b81a8b698
Step 6 : COPY templates/index.html /usr/src/app/templates/
 ---> 8098386bee99
Removing intermediate container b783d7646f83
Step 7 : EXPOSE 5000
 ---> Running in 31401b7dea40
 ---> 5e9988d87da7
Removing intermediate container 31401b7dea40
Step 8 : CMD python /usr/src/app/app.py
 ---> Running in 78e324d26576
 ---> 2f7357a0805d
Removing intermediate container 78e324d26576
Successfully built 2f7357a0805d
```

If you don't have the `alpine:3.5` image, the client will first pull the image and then create your image. Therefore, your output on running the command will look different from mine. If everything went well, your image should be ready! Run `docker images` and see if your image (`<YOUR_USERNAME>/myfirstapp`) shows.

### 2.3.4 Run your image
The next step in this section is to run the image and see if it actually works.

```bash
$ docker run -p 8888:5000 --name myfirstapp YOUR_USERNAME/myfirstapp
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
```

Head over to `http://localhost:8888` and your app should be live. **Note** If you are using Docker Machine, you may need to open up another terminal and determine the container ip address using `docker-machine ip default`.

<img src="../images/catgif.png" title="static">

Hit the Refresh button in the web browser to see a few more cat images.

### 2.3.4 Push your image
Now that you've created and tested your image, you can push it to [Docker Cloud](https://cloud.docker.com).

First you have to login to your Docker Cloud account, to do that:

```bash
docker login
```

Enter `YOUR_USERNAME` and `password` when prompted. 

Now all you have to do is:

```bash
docker push YOUR_USERNAME/myfirstapp
```

Now that you are done with this container, stop and remove it since you won't be using it again.

Open another terminal window and execute the following commands:

```bash
$ docker stop myfirstapp
$ docker rm myfirstapp
```

or

```bash
$ docker rm -f myfirstapp
```

### 2.3.5 Dockerfile commands summary

Here's a quick summary of the few basic commands we used in our Dockerfile.

* `FROM` starts the Dockerfile. It is a requirement that the Dockerfile must start with the `FROM` command. Images are created in layers, which means you can use another image as the base image for your own. The `FROM` command defines your base layer. As arguments, it takes the name of the image. Optionally, you can add the Docker Cloud username of the maintainer and image version, in the format `username/imagename:version`.

* `RUN` is used to build up the Image you're creating. For each `RUN` command, Docker will run the command then create a new layer of the image. This way you can roll back your image to previous states easily. The syntax for a `RUN` instruction is to place the full text of the shell command after the `RUN` (e.g., `RUN mkdir /user/local/foo`). This will automatically run in a `/bin/sh` shell. You can define a different shell like this: `RUN /bin/bash -c 'mkdir /user/local/foo'`

* `COPY` copies local files into the container.

* `CMD` defines the commands that will run on the Image at start-up. Unlike a `RUN`, this does not create a new layer for the Image, but simply runs the command. There can only be one `CMD` per a Dockerfile/Image. If you need to run multiple commands, the best way to do that is to have the `CMD` run a script. `CMD` requires that you tell it where to run the command, unlike `RUN`. So example `CMD` commands would be:

```
  CMD ["python", "./app.py"]

  CMD ["/bin/bash", "echo", "Hello World"]
```

* `EXPOSE` creates a hint for users of an image which ports provide services. It is included in the information which
 can be retrieved via `$ docker inspect <container-id>`.     

>**Note:** The `EXPOSE` command does not actually make any ports accessible to the host! Instead, this requires 
publishing ports by means of the `-p` flag when using `$ docker run`.  

* `PUSH` pushes your image to Docker Cloud, or alternately to a [private registry](https://docs.docker.com/registry/)

>**Note:** If you want to learn more about Dockerfiles, check out [Best practices for writing Dockerfiles](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/).

## Next Steps
For the next step in the tutorial head over to [3.0 Deploying an app to a Swarm](./votingapp.md)
