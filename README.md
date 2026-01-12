<div align="center"><img src="https://github.com/vtiquet/holbertonschool-resources/blob/main/image/Holberton-Logo.svg" width=40% height=40%/></div>

# Docker

## Table of Contents :

  - [0. Create Your First Docker Image](#subparagraph0)
  - [1. Back-end](#subparagraph1)
  - [2. Front-end](#subparagraph2)
  - [3. Connecting the Front-end and Back-end](#subparagraph3)
  - [4. Making it Simpler with Docker Compose](#subparagraph4)
  - [5. Proxy Server](#subparagraph5)
  - [6. Scale Horizontally](#subparagraph6)

## Task
### 0. Create Your First Docker Image <a name='subparagraph0'></a>

To create a Docker image, you will need to utilize a Dockerfile. Create a Dockerfile that:

* <p>Is based on the latest ubuntu</p>
* <p>Update APT using apt-get update</p>
* <p>Upgrade currently installed software through APT using apt-get upgrade -y</p>
* <p>Once built, you can run the Docker image in a container and it will echo “Hello, World!” on the terminal.</p>

Example (your output may look different depending on your local environment and whether or not you have cached data):

<strong>Terminal</strong>

```
Dereks-MacBook-Pro:docker-project derekwebb$ docker build -f ./Dockerfile -t softy-pinko:task0 .
[+] Building 0.7s (7/7) FINISHED                                                                  
 => [internal] load build definition from Dockerfile                                         0.0s
 => => transferring dockerfile: 37B                                                          0.0s
 => [internal] load .dockerignore                                                            0.0s
 => => transferring context: 2B                                                              0.0s
 => [internal] load metadata for docker.io/library/ubuntu:latest                             0.6s
 => [1/3] FROM docker.io/library/ubuntu:latest@sha256:dfd64a3b4296d8c9b62aa3309984f8620b98d  0.0s
 => CACHED [2/3] RUN apt-get update                                                          0.0s
 => CACHED [3/3] RUN apt-get upgrade -y                                                      0.0s
 => exporting to image                                                                       0.0s
 => => exporting layers                                                                      0.0s
 => => writing image sha256:45d461b2a1471047589659e82af46202206be08b5b725d941a0a659b843a402  0.0s
 => => naming to docker.io/library/softy-pinko:task0                                         0.0s
Dereks-MacBook-Pro:docker-project derekwebb$ docker run -it --rm --name softy-pinko-task0 softy-pinko:task0
Hello, World!
Dereks-MacBook-Pro:docker-project derekwebb$
```

---

### 1. Back-end <a name='subparagraph1'></a>

For this task, start by making a copy of your <code>task0</code> directory and name it <code>task1</code>. Next, we want to change the <code>Dockerfile</code> to install Python3, pip3, and Flask. You may not have used Flask, yet, but not to worry; for this project, we will give you all of the Flask code you need to get started. We’ll validate that all have been installed correctly by running a Flask server with one endpoint that when called returns “Hello, World!”

* <p>Install <code>python3</code>, <code>python3-pip</code>, and <code>flask</code></p>

  * Note: Make sure to automatically install and skip user input with the <code>-y</code> flag for <code>apt-get</code>
  * Note: flask must be installed with <code>pip3</code>, <strong>not through apt-get</strong>

If you get a <code>This environment is externally managed</code> error when trying to install Python packages, add the following line before calling <code>pip</code> on your <code>Dockerfile</code>

> <code>RUN rm /usr/lib/python*/EXTERNALLY-MANAGED</code>

* <p>Locally, create a Python file named <code>api.py</code> and paste the following Python script - it uses Flask to create one endpoint that returns “Hello, World!” when called</p>

  * Hosting this Flask app on <code>0.0.0.0</code> instead of <code>127.0.0.1</code> means that it is reachable outside of the current machine (the current machine being a Docker container which is running inside of your laptop/desktop)
  * Host this Flask app on port 5252

<strong>api.py</strong>

```
from flask import Flask

app = Flask(__name__)

@app.route('/api/hello')
def hello_world():
    return 'Hello, World!'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5252)
```

* <p>In your <code>Dockerfile</code>, use <code>/app</code> as the working directory and copy the Python file to your Docker image</p>
* <p>When running your Docker image, your Flask server should spin up and accept requests</p>

  * You will need to make sure that you forward the Docker container’s port 5252 to the host machine’s port 5252 when running your image in a container.
* <p>Example (your output may look different depending on your local environment and whether or not you have cached data):</p>

<strong>Terminal</strong>

```
Dereks-MacBook-Pro:task1 derekwebb$ docker build -f ./Dockerfile -t softy-pinko:task1 .
[+] Building 0.9s (12/12) FINISHED                                                                
 => [internal] load build definition from Dockerfile                                         0.0s
 => => transferring dockerfile: 37B                                                          0.0s
 => [internal] load .dockerignore                                                            0.0s
 => => transferring context: 2B                                                              0.0s
 => [internal] load metadata for docker.io/library/ubuntu:latest                             0.8s
 => [internal] load build context                                                            0.0s
 => => transferring context: 28B                                                             0.0s
 => [1/7] FROM docker.io/library/ubuntu:latest@sha256:ac58ff7fe25edc58bdf0067ca99df00014dbd  0.0s
 => CACHED [2/7] RUN apt-get update                                                          0.0s
 => CACHED [3/7] RUN apt-get upgrade -y                                                      0.0s
 => CACHED [4/7] RUN apt-get install -y python3 python3-pip                                  0.0s
 => CACHED [5/7] RUN pip3 install flask                                                      0.0s
 => CACHED [6/7] WORKDIR /app                                                                0.0s
 => CACHED [7/7] COPY ./api.py /app/api.py                                                   0.0s
 => exporting to image                                                                       0.0s
 => => exporting layers                                                                      0.0s
 => => writing image sha256:58f5eb04ef4a3ac604fcc74adc799c09e09b2697675d9ec552d45c3a9e7d572  0.0s
 => => naming to docker.io/library/softy-pinko:task1                                         0.0s
Dereks-MacBook-Pro:task1 derekwebb$ docker run -p 5252:5252 -it --rm --name softy-pinko-task1 softy-pinko:task1
 * Serving Flask app 'api'
 * Debug mode: off
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on all addresses (0.0.0.0)
 * Running on http://127.0.0.1:5252
 * Running on http://172.17.0.2:5252
Press CTRL+C to quit
```

<strong>Browser</strong>

<img alt="" loading="lazy" src="https://drive.google.com/uc?id=1thSkdrvRD7MYO1A7DJx73dzA6JygUZ-b" style=""/>

(If the image above does not load, go to <a href="/rltoken/tPc9Olv6PjcxSp8zi6IzfQ" target="_blank" title="https://drive.google.com/uc?id=1thSkdrvRD7MYO1A7DJx73dzA6JygUZ-b">https://drive.google.com/uc?id=1thSkdrvRD7MYO1A7DJx73dzA6JygUZ-b</a>)

---

### 2. Front-end <a name='subparagraph2'></a>

For this task, start by making a copy of your <code>task1</code> directory and name it <code>task2</code>.  We have created a very simple API server with one route that returns “Hello, World!” and now we want to create a web page to view content from our API server in the context of a more full front-end. Before creating our front end, let’s reorganize this project a bit in our <code>task2</code> directory.

* <p>Create a new directory named <code>back-end</code> inside of your <code>task2</code> directory.</p>
* <p>Move all of the files currently in <code>task2</code> inside of the new <code>back-end</code> directory. </p>

> You should have <code>api.py</code> and <code>Dockerfile</code> inside of your new <code>task2/back-end</code> directory.

* <p>Create a new <code>task2/front-end</code> directory</p>
* <p>Inside your new <code>task2/front-end</code>, clone this repository -&gt; <a href="/rltoken/nj1q-0ViTkp0E4mCwT4fNg" target="_blank" title="https://github.com/atlas-school/softy-pinko-front-end">https://github.com/atlas-school/softy-pinko-front-end</a></p>

With the <code>softy-pinko-front-end</code> directory and files in place, we need to create a new <code>Dockerfile</code> in your <code>task2/front-end</code> directory.

In order to host and distribute our front-end content we will use a static web server named <a href="/rltoken/PtxALAzljP8pkMygn-j7dw" target="_blank" title="Nginx">Nginx</a>; there are many others that can be used, but this one is rather simple to get started with and conveniently has a Docker image that we can use which is pre-installed.

* <p>In the new <code>task2/front-end/Dockerfile</code>, instead of using the latest ubuntu version, use the latest version of <code>nginx</code>.</p>
* <p>Your <code>softy-pinko-front-end files</code> must be copied to <code>/var/www/html/softy-pinko-front-end</code> on the Docker image. </p>
* <p>Create an Nginx config file named <code>softy-pinko-front-end.conf</code> inside of the <code>task2/front-end</code> directory. This file must be copied to the Docker image to <code>/etc/nginx/conf.d/default.conf</code> and must include all of the configuration settings required to get your site to show up when visiting the URL. </p>

> When researching Nginx config files, the only section you’ll need in the <code>softy-pinko-front-end.conf</code> file is the “server” section. Pay attention to the syntax used to set up a port to listen to (recommendation: port 9000), the name of the server, the location, and the index file to use.

<strong>softy-pinko-front-end.conf</strong>

```
server {
// Replace with your Nginx server configuration
}
```

At the end of this process, you should have a front end that is accessible like the following:

<strong>Terminal</strong>

```
Dereks-MacBook-Pro:task2 derekwebb$ docker build -f ./front-end/Dockerfile -t softy-pinko-front-end:task2 ./front-end
[+] Building 0.6s (8/8) FINISHED                                                                                                          
 => [internal] load build definition from Dockerfile                                                                                 0.0s
 => => transferring dockerfile: 37B                                                                                                  0.0s
 => [internal] load .dockerignore                                                                                                    0.0s
 => => transferring context: 2B                                                                                                      0.0s
 => [internal] load metadata for docker.io/library/nginx:latest                                                                      0.5s
 => [internal] load build context                                                                                                    0.0s
 => => transferring context: 34.13kB                                                                                                 0.0s
 => [1/3] FROM docker.io/library/nginx:latest@sha256:af296b188c7b7df99ba960ca614439c99cb7cf252ed7bbc23e90cfda59092305                0.0s
 => CACHED [2/3] COPY ./softy-pinko-front-end /var/www/html/softy-pinko-front-end                                                    0.0s
 => CACHED [3/3] COPY ./softy-pinko-front-end.conf  /etc/nginx/conf.d/default.conf                                                   0.0s
 => exporting to image                                                                                                               0.0s
 => => exporting layers                                                                                                              0.0s
 => => writing image sha256:5aeebcbf58006d92aa190103a44fa395f2e51a42cc7452a3561ce42af3b2aa46                                         0.0s
 => => naming to docker.io/library/softy-pinko-front-end:task2                                                                       0.0s
Dereks-MacBook-Pro:task2 derekwebb$ docker run -p 9000:9000 -it --rm --name softy-pinko-front-end-task2 softy-pinko-front-end:task2
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: /etc/nginx/conf.d/default.conf differs from the packaged version
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2023/06/12 17:00:32 [notice] 1#1: using the "epoll" event method
2023/06/12 17:00:32 [notice] 1#1: nginx/1.25.0
2023/06/12 17:00:32 [notice] 1#1: built by gcc 10.2.1 20210110 (Debian 10.2.1-6) 
2023/06/12 17:00:32 [notice] 1#1: OS: Linux 5.15.49-linuxkit
2023/06/12 17:00:32 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2023/06/12 17:00:32 [notice] 1#1: start worker processes
2023/06/12 17:00:32 [notice] 1#1: start worker process 28
2023/06/12 17:00:32 [notice] 1#1: start worker process 29
2023/06/12 17:00:32 [notice] 1#1: start worker process 30
2023/06/12 17:00:32 [notice] 1#1: start worker process 31
2023/06/12 17:00:32 [notice] 1#1: start worker process 32
2023/06/12 17:00:32 [notice] 1#1: start worker process 33
2023/06/12 17:00:32 [notice] 1#1: start worker process 34
2023/06/12 17:00:32 [notice] 1#1: start worker process 35
```

<strong>Browser</strong>

<img alt="" loading="lazy" src="https://drive.google.com/uc?id=1UsHN_iZUYFczTVYZ_jSiWbTlCWmZ-2Of" style=""/>

If the image above does not load, go to <a href="/rltoken/iqtDPdPtxEi2ib6UTrZFBQ" target="_blank" title="https://drive.google.com/file/d/125rrSKiRI2whr4Uv9ydnJ5ZpcYWCIixQ">https://drive.google.com/file/d/125rrSKiRI2whr4Uv9ydnJ5ZpcYWCIixQ</a>

---

### 3. Connecting the Front-end and Back-end <a name='subparagraph3'></a>

To start this task, make a copy of your <code>task2</code> directory and name it <code>task3</code>.

This task will have you connect your front-end to the back-end allowing you to have dynamic data on your front-end. This means that communication will occur between your two Docker images (each of which will be running in their own Docker container). To facilitate this, be sure to have multiple terminal instances open so you can have one Docker container running on each.

First thing we should do is prepare the front-end. We need to add a couple of things:

<strong>New HTML to add</strong>

```

```

This needs to be added before the <code>&lt;h1&gt;</code> that includes the text “We provide the best strategy to grow up your business”.

<strong>index.html</strong>

```
// . . . SOME HTML BEFORE . . . 

    
    We provide the best strategyto grow up yourbusiness
    Discover More

// . . . SOME HTML AFTER . . .
```

This new <code>&lt;h1&gt;</code> tag with the ID of <code>dynamic-content</code> is the place we’re going to insert dynamic data from our API server.

Now we need to actually use some JavaScript code to make the request. Place the following script near the bottom of the HTML as the last <code>&lt;script&gt;</code> tag before the closing <code>&lt;/body&gt;</code> tag.

<strong>Add this script to your index.html</strong>

```

```

That is all the front-end needs to be able to connect to the back-end, however there is one more thing we must do to our back-end so that it can accept cross-origin requests from our front-end. We must use the CORS plugin for Flask.

In your back-end’s <code>back-end/Dockerfile</code>, use <code>pip3</code> to install <code>flask-cors</code> after you have installed <code>flask</code> through <code>pip3</code>. Next, use this updated python script as your <code>api.py</code>.

<strong>api.py</strong>

```
from flask import Flask
from flask_cors import CORS

app = Flask(__name__)
CORS(app)

@app.route('/api/hello')
def hello_world():
    return 'Hello, World!'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5252)
```

Now, your back-end will allow your front-end to communicate with it even though they are not on the same server (they are on two different Docker containers).

It should look like this:

<strong>First terminal building/running the backend:</strong>

```
Dereks-MacBook-Pro:task3 derekwebb$ docker build -f ./back-end/Dockerfile -t softy-pinko-back-end:task3 ./back-end
[+] Building 1.2s (13/13) FINISHED                                                                                                                                                                                                
 => [internal] load build definition from Dockerfile                                                                                                                                                                         0.0s
 => => transferring dockerfile: 37B                                                                                                                                                                                          0.0s
 => [internal] load .dockerignore                                                                                                                                                                                            0.0s
 => => transferring context: 2B                                                                                                                                                                                              0.0s
 => [internal] load metadata for docker.io/library/ubuntu:latest                                                                                                                                                             1.1s
 => [1/8] FROM docker.io/library/ubuntu:latest@sha256:ac58ff7fe25edc58bdf0067ca99df00014dbd032e2246d30a722fa348fd799a5                                                                                                       0.0s
 => [internal] load build context                                                                                                                                                                                            0.0s
 => => transferring context: 28B                                                                                                                                                                                             0.0s
 => CACHED [2/8] RUN apt-get update                                                                                                                                                                                          0.0s
 => CACHED [3/8] RUN apt-get upgrade -y                                                                                                                                                                                      0.0s
 => CACHED [4/8] RUN apt-get install -y python3 python3-pip                                                                                                                                                                  0.0s
 => CACHED [5/8] RUN pip3 install flask                                                                                                                                                                                      0.0s
 => CACHED [6/8] RUN pip3 install flask-cors                                                                                                                                                                                 0.0s
 => CACHED [7/8] WORKDIR /app                                                                                                                                                                                                0.0s
 => CACHED [8/8] COPY ./api.py /app/api.py                                                                                                                                                                                   0.0s
 => exporting to image                                                                                                                                                                                                       0.0s
 => => exporting layers                                                                                                                                                                                                      0.0s
 => => writing image sha256:72111d9280c3fb3d875aa956a9d5eb407adc55256e667ec3e30dcadd128fd073                                                                                                                                 0.0s
 => => naming to docker.io/library/softy-pinko-back-end:task3                                                                                                                                                                0.0s
Dereks-MacBook-Pro:task3 derekwebb$ docker run -p 5252:5252 -it --rm --name softy-pinko-back-end-task3 softy-pinko-back-end:task3
 * Serving Flask app 'api'
 * Debug mode: off
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on all addresses (0.0.0.0)
 * Running on http://127.0.0.1:5252
 * Running on http://172.17.0.2:5252
Press CTRL+C to quit
```

<strong>Second terminal building/running the front-end:</strong>

```
Dereks-MacBook-Pro:task3 derekwebb$ docker build -f ./front-end/Dockerfile -t softy-pinko-front-end:task3 ./front-end
[+] Building 1.2s (8/8) FINISHED                                                                                                                                                                                                  
 => [internal] load build definition from Dockerfile                                                                                                                                                                         0.0s
 => => transferring dockerfile: 37B                                                                                                                                                                                          0.0s
 => [internal] load .dockerignore                                                                                                                                                                                            0.0s
 => => transferring context: 2B                                                                                                                                                                                              0.0s
 => [internal] load metadata for docker.io/library/nginx:latest                                                                                                                                                              1.0s
 => CACHED [1/3] FROM docker.io/library/nginx:latest@sha256:af296b188c7b7df99ba960ca614439c99cb7cf252ed7bbc23e90cfda59092305                                                                                                 0.0s
 => [internal] load build context                                                                                                                                                                                            0.0s
 => => transferring context: 34.59kB                                                                                                                                                                                         0.0s
 => [2/3] COPY ./softy-pinko-front-end /var/www/html/softy-pinko-front-end                                                                                                                                                   0.0s
 => [3/3] COPY ./softy-pinko-front-end.conf  /etc/nginx/conf.d/default.conf                                                                                                                                                  0.0s
 => exporting to image                                                                                                                                                                                                       0.0s
 => => exporting layers                                                                                                                                                                                                      0.0s
 => => writing image sha256:f981f38fb967b1a4d477ce370ed52e1fcd97e1dc4863baf1faf47e908bec4057                                                                                                                                 0.0s
 => => naming to docker.io/library/softy-pinko-front-end:task3                                                                                                                                                               0.0s
Dereks-MacBook-Pro:task3 derekwebb$ docker run -p 9000:9000 -it --rm --name softy-pinko-front-end-task3 softy-pinko-front-end:task3
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: /etc/nginx/conf.d/default.conf differs from the packaged version
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2023/06/12 19:10:39 [notice] 1#1: using the "epoll" event method
2023/06/12 19:10:39 [notice] 1#1: nginx/1.25.0
2023/06/12 19:10:39 [notice] 1#1: built by gcc 10.2.1 20210110 (Debian 10.2.1-6) 
2023/06/12 19:10:39 [notice] 1#1: OS: Linux 5.15.49-linuxkit
2023/06/12 19:10:39 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2023/06/12 19:10:39 [notice] 1#1: start worker processes
2023/06/12 19:10:39 [notice] 1#1: start worker process 28
2023/06/12 19:10:39 [notice] 1#1: start worker process 29
2023/06/12 19:10:39 [notice] 1#1: start worker process 30
2023/06/12 19:10:39 [notice] 1#1: start worker process 31
2023/06/12 19:10:39 [notice] 1#1: start worker process 32
2023/06/12 19:10:39 [notice] 1#1: start worker process 33
2023/06/12 19:10:39 [notice] 1#1: start worker process 34
2023/06/12 19:10:39 [notice] 1#1: start worker process 35
```

<strong>Browser</strong>

<img alt="" loading="lazy" src="https://drive.google.com/uc?id=1RajWcURoyl_O4-2IbigYn2T2940u67v1" style=""/>

If the image above does not load, go to <a href="/rltoken/turVBovHAKLdPLJwGSd2XA" target="_blank" title="https://drive.google.com/uc?id=18x9WulYaB3QTAxIsZbnypL3L95UGYkWc">https://drive.google.com/uc?id=18x9WulYaB3QTAxIsZbnypL3L95UGYkWc</a>

---

### 4. Making it Simpler with Docker Compose <a name='subparagraph4'></a>

Having separate Docker images/containers per component of your application can reduce the complexity of your web apps. That said, having more than a single Docker image that you need to spin up in containers can present challenges; what if you need to spin up 3 distinct Docker images, or 7, or 50? Opening each one, one at a time would quickly become an issue. That’s where Docker Compose comes into play. With a <code>docker-compose.yml</code> file, we can specify the different components of your entire application, set up some basic configurations, and allow Docker to handle spinning up the entire application all at once, no matter how many containers there are.

Before going further, be sure to copy the <code>task3</code> directory and name it <code>task4</code>.

Inside the <code>task4</code> directory, create a <code>docker-compose.yml</code> file

Once you’ve set up your <code>docker-compose.yml</code> file with the correct services, you should be able to build them using <code>docker-compose build</code> and run it with <code>docker-compose up</code> like this:

<strong>Terminal</strong>

```
Dereks-MacBook-Pro:task4 derekwebb$ docker-compose build
[+] Building 1.3s (13/13) FINISHED                                                                                              
 => [internal] load build definition from Dockerfile                                                                       0.0s
 => => transferring dockerfile: 32B                                                                                        0.0s
 => [internal] load .dockerignore                                                                                          0.0s
 => => transferring context: 2B                                                                                            0.0s
 => [internal] load metadata for docker.io/library/ubuntu:latest                                                           1.2s
 => [1/8] FROM docker.io/library/ubuntu:latest@sha256:ac58ff7fe25edc58bdf0067ca99df00014dbd032e2246d30a722fa348fd799a5     0.0s
 => [internal] load build context                                                                                          0.0s
 => => transferring context: 28B                                                                                           0.0s
 => CACHED [2/8] RUN apt-get update                                                                                        0.0s
 => CACHED [3/8] RUN apt-get upgrade -y                                                                                    0.0s
 => CACHED [4/8] RUN apt-get install -y python3 python3-pip                                                                0.0s
 => CACHED [5/8] RUN pip3 install flask                                                                                    0.0s
 => CACHED [6/8] RUN pip3 install flask-cors                                                                               0.0s
 => CACHED [7/8] WORKDIR /app                                                                                              0.0s
 => CACHED [8/8] COPY ./api.py /app/api.py                                                                                 0.0s
 => exporting to image                                                                                                     0.0s
 => => exporting layers                                                                                                    0.0s
 => => writing image sha256:26379ebe4741c491978aa03c623ebbd768c964fefba64055e3013460fe7c5a1d                               0.0s
 => => naming to docker.io/library/softy-pinko-back-end:task4                                                              0.0s
[+] Building 1.0s (8/8) FINISHED                                                                                                
 => [internal] load build definition from Dockerfile                                                                       0.0s
 => => transferring dockerfile: 32B                                                                                        0.0s
 => [internal] load .dockerignore                                                                                          0.0s
 => => transferring context: 2B                                                                                            0.0s
 => [internal] load metadata for docker.io/library/nginx:latest                                                            0.8s
 => [internal] load build context                                                                                          0.1s
 => => transferring context: 3.46kB                                                                                        0.0s
 => [1/3] FROM docker.io/library/nginx:latest@sha256:af296b188c7b7df99ba960ca614439c99cb7cf252ed7bbc23e90cfda59092305      0.0s
 => CACHED [2/3] COPY ./softy-pinko-front-end /var/www/html/softy-pinko-front-end                                          0.0s
 => CACHED [3/3] COPY ./softy-pinko-front-end.conf  /etc/nginx/conf.d/default.conf                                         0.0s
 => exporting to image                                                                                                     0.1s
 => => exporting layers                                                                                                    0.0s
 => => writing image sha256:479f9d992a6eae6a56dff4b0cf7f9754446fa195ef273afba5705f460fee8da4                               0.0s
 => => naming to docker.io/library/softy-pinko-front-end:task4                                                             0.0s
Dereks-MacBook-Pro:task4 derekwebb$ 
Dereks-MacBook-Pro:task4 derekwebb$ docker-compose up
[+] Running 0/2
 ⠿ front-end Warning                                                                                                                                                                                                                               2.1s
 ⠿ back-end Warning                                                                                                                                                                                                                                2.1s
[+] Building 1.6s (20/20) FINISHED                                                                                                                                                                                                                      
 => [softy-pinko-front-end:task4 internal] load build definition from Dockerfile                                                                                                                                                                   0.0s
 => => transferring dockerfile: 188B                                                                                                                                                                                                               0.0s
 => [softy-pinko-back-end:task4 internal] load build definition from Dockerfile                                                                                                                                                                    0.0s
 => => transferring dockerfile: 262B                                                                                                                                                                                                               0.0s
 => [softy-pinko-front-end:task4 internal] load .dockerignore                                                                                                                                                                                      0.0s
 => => transferring context: 2B                                                                                                                                                                                                                    0.0s
 => [softy-pinko-back-end:task4 internal] load .dockerignore                                                                                                                                                                                       0.0s
 => => transferring context: 2B                                                                                                                                                                                                                    0.0s
 => [softy-pinko-front-end:task4 internal] load metadata for docker.io/library/nginx:latest                                                                                                                                                        1.4s
 => [softy-pinko-back-end:task4 internal] load metadata for docker.io/library/ubuntu:latest                                                                                                                                                        1.4s
 => [softy-pinko-back-end:task4 internal] load build context                                                                                                                                                                                       0.0s
 => => transferring context: 250B                                                                                                                                                                                                                  0.0s
 => [softy-pinko-back-end:task4 1/8] FROM docker.io/library/ubuntu:latest@sha256:ac58ff7fe25edc58bdf0067ca99df00014dbd032e2246d30a722fa348fd799a5                                                                                                  0.0s
 => [softy-pinko-front-end:task4 internal] load build context                                                                                                                                                                                      0.1s
 => => transferring context: 2.68MB                                                                                                                                                                                                                0.0s
 => [softy-pinko-front-end:task4 1/3] FROM docker.io/library/nginx:latest@sha256:af296b188c7b7df99ba960ca614439c99cb7cf252ed7bbc23e90cfda59092305                                                                                                  0.0s
 => CACHED [softy-pinko-back-end:task4 2/8] RUN apt-get update                                                                                                                                                                                     0.0s
 => CACHED [softy-pinko-back-end:task4 3/8] RUN apt-get upgrade -y                                                                                                                                                                                 0.0s
 => CACHED [softy-pinko-back-end:task4 4/8] RUN apt-get install -y python3 python3-pip                                                                                                                                                             0.0s
 => CACHED [softy-pinko-back-end:task4 5/8] RUN pip3 install flask                                                                                                                                                                                 0.0s
 => CACHED [softy-pinko-back-end:task4 6/8] RUN pip3 install flask-cors                                                                                                                                                                            0.0s
 => CACHED [softy-pinko-back-end:task4 7/8] WORKDIR /app                                                                                                                                                                                           0.0s
 => CACHED [softy-pinko-back-end:task4 8/8] COPY ./api.py /app/api.py                                                                                                                                                                              0.0s
 => [softy-pinko-front-end:task4] exporting to image                                                                                                                                                                                               0.0s
 => => exporting layers                                                                                                                                                                                                                            0.0s
 => => writing image sha256:26379ebe4741c491978aa03c623ebbd768c964fefba64055e3013460fe7c5a1d                                                                                                                                                       0.0s
 => => naming to docker.io/library/softy-pinko-back-end:task4                                                                                                                                                                                      0.0s
 => => writing image sha256:479f9d992a6eae6a56dff4b0cf7f9754446fa195ef273afba5705f460fee8da4                                                                                                                                                       0.0s
 => => naming to docker.io/library/softy-pinko-front-end:task4                                                                                                                                                                                     0.0s
 => CACHED [softy-pinko-front-end:task4 2/3] COPY ./softy-pinko-front-end /var/www/html/softy-pinko-front-end                                                                                                                                      0.0s
 => CACHED [softy-pinko-front-end:task4 3/3] COPY ./softy-pinko-front-end.conf  /etc/nginx/conf.d/default.conf                                                                                                                                     0.0s
[+] Running 3/3
 ⠿ Network task4_default        Created                                                                                                                                                                                                            0.0s
 ⠿ Container task4-back-end-1   Created                                                                                                                                                                                                            0.1s
 ⠿ Container task4-front-end-1  Created                                                                                                                                                                                                            0.1s
Attaching to task4-back-end-1, task4-front-end-1
task4-back-end-1   |  * Serving Flask app 'api'
task4-back-end-1   |  * Debug mode: off
task4-back-end-1   | WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
task4-back-end-1   |  * Running on all addresses (0.0.0.0)
task4-back-end-1   |  * Running on http://127.0.0.1:5252
task4-back-end-1   |  * Running on http://172.18.0.2:5252
task4-back-end-1   | Press CTRL+C to quit
task4-front-end-1  | /docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
task4-front-end-1  | /docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
task4-front-end-1  | /docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
task4-front-end-1  | 10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
task4-front-end-1  | 10-listen-on-ipv6-by-default.sh: info: /etc/nginx/conf.d/default.conf differs from the packaged version
task4-front-end-1  | /docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
task4-front-end-1  | /docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
task4-front-end-1  | /docker-entrypoint.sh: Configuration complete; ready for start up
task4-front-end-1  | 2023/06/12 19:27:43 [notice] 1#1: using the "epoll" event method
task4-front-end-1  | 2023/06/12 19:27:43 [notice] 1#1: nginx/1.25.0
task4-front-end-1  | 2023/06/12 19:27:43 [notice] 1#1: built by gcc 10.2.1 20210110 (Debian 10.2.1-6) 
task4-front-end-1  | 2023/06/12 19:27:43 [notice] 1#1: OS: Linux 5.15.49-linuxkit
task4-front-end-1  | 2023/06/12 19:27:43 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
task4-front-end-1  | 2023/06/12 19:27:43 [notice] 1#1: start worker processes
task4-front-end-1  | 2023/06/12 19:27:43 [notice] 1#1: start worker process 28
task4-front-end-1  | 2023/06/12 19:27:43 [notice] 1#1: start worker process 29
task4-front-end-1  | 2023/06/12 19:27:43 [notice] 1#1: start worker process 30
task4-front-end-1  | 2023/06/12 19:27:43 [notice] 1#1: start worker process 31
task4-front-end-1  | 2023/06/12 19:27:43 [notice] 1#1: start worker process 32
task4-front-end-1  | 2023/06/12 19:27:43 [notice] 1#1: start worker process 33
task4-front-end-1  | 2023/06/12 19:27:43 [notice] 1#1: start worker process 34
task4-front-end-1  | 2023/06/12 19:27:43 [notice] 1#1: start worker process 35
```

Important keywords to have in your <code>docker-compose.yml</code> file:

* <p>services</p>
* <p>build, context, dockerfile</p>
* <p>image</p>
* <p>ports</p>

  * Note: when setting a port, the syntax will be HOST<em>PORT:CONTAINER</em>PORT. For example, <code>9999:4567</code> would map the 4567 port inside of the Docker container to the host machine’s 9999 port. If you only supply one port number, instead of a mapping, you are only specifying an internal port that other Docker containers can reach, but they will not be mapped to any port on the host machine. If you do not map any ports, you will not be able to get past the firewall that Docker has put up. Docker uses something similar to Linux’s Uncomplicated Firewall ufw; you do not directly work with this firewall, but instead, map allowed ports in Dockerfiles or docker-compose files to allow/disallow access through certain ports.
* <p>depends_on</p>

---

### 5. Proxy Server <a name='subparagraph5'></a>

So far we’ve created a static server and one API server, each of which has its own Docker images and runs in different Docker containers. This is okay, but this means that we have to keep track of two separate locations. If we change the port that the API server is using, then we’d have to change the client to hit that new port; in this case ports on our computer, but it would be IP addresses if these were external servers. This is not ideal, especially if you have many different clients that you’d need to update and deploy.

Instead, let’s put a server in front of our static server and API server that acts as a proxy between the client and our full application. Clients (in this case the web browser) will only have to know about the proxy server’s location and our front-end can also simply hit the same proxy server, rather than going to the API directly, to then be routed to the API server in order to get dynamic data from it.

Before going further, copy the <code>task4</code> directory and name it <code>task5</code>. In the <code>task5</code> directory, create a new folder named <code>proxy</code>.

Just like with our static-content server, we’re going to use Nginx for this proxy server. In your <code>proxy</code> directory, create a <code>Dockerfile</code> that uses the latest version of nginx. Also, create a file named <code>proxy.conf</code>, which will contain all of the configuration for your proxy server. In the <code>Dockerfile</code>, make sure to copy the <code>proxy.conf</code> file to this specific location in your Docker image for the proxy server: <code>/etc/nginx/conf.d/default.conf</code>.

For the <code>proxy.conf</code> file, the only section you’ll need in the <code>proxy.conf</code> file is the “server” section. You want this proxy server to be listening to port 80. Set up a <code>location</code> section for <code>/</code> and use <code>proxy_pass</code> to route any request coming in on that route to <code>http://INSERT-YOUR-FRONT-END-DOCKER-COMPOSE-SERVICE-NAME-HERE:9000</code>. Next, set up a <code>location</code> section for <code>/api</code> and use <code>proxy_pass</code> to route any request coming in on that route to <code>http://INSERT-YOUR-BACK-END-DOCKER-COMPOSE-SERVICE-NAME-HERE:5252</code>.

<strong>proxy.conf</strong>

```
server {
    // Replace with your Nginx server configuration
}
```

Note that you are using the same service name that you used in your <code>docker-compose.yml</code> file for the front and back ends. This is because Docker has an internal DNS that will setup routes to internal IP addresses for those names. This is a bit of “magic” that Docker does, but it makes it super convenient to not have to know the exact IP addresses of these services.

Another thing that needs to be modified is the JavaScript that is used. It was previously calling the back-end server directly; instead, we want to have the JavaScript make an API call through the proxy server as well. Replace your JavaScript at the bottom of the <code>index.html</code> file with the following:

<strong>JavaScript At Bottom Of index.html</strong>

```

```

Next, we need to update the <code>docker-compose.yml</code> file to add a new service named <code>proxy</code>. Be sure to include the same sections as previously used:

* <p>build, context, dockerfile</p>
* <p>image</p>
* <p>ports</p>

  * Map the container’s port 80 to the host machine’s port 80.
  * Note: You should remove the mapping for the front-end and back-end service to the host machine ports. You will want to have port 5252 and port 9000 open internally on the containers so that other Docker containers can reach them, but you do not want external clients reaching out directly to the front-end or the back-end; everything should go through the proxy server. If you do not map any ports, you will not be able to get past the firewall that Docker has put up. Docker uses something similar to Linux’s Uncomplicated Firewall ufw; you do not directly work with this firewall, but instead, map allowed ports in Dockerfiles or docker-compose files to allow/disallow access through certain ports.

depends_on

Your output should look something like this:

<strong>Terminal</strong>

```
Dereks-MacBook-Pro:task5 derekwebb$ docker-compose build
[+] Building 1.5s (13/13) FINISHED                                                                                              
 => [internal] load build definition from Dockerfile                                                                       0.0s
 => => transferring dockerfile: 262B                                                                                       0.0s
 => [internal] load .dockerignore                                                                                          0.0s
 => => transferring context: 2B                                                                                            0.0s
 => [internal] load metadata for docker.io/library/ubuntu:latest                                                           1.4s
 => [1/8] FROM docker.io/library/ubuntu:latest@sha256:ac58ff7fe25edc58bdf0067ca99df00014dbd032e2246d30a722fa348fd799a5     0.0s
 => [internal] load build context                                                                                          0.0s
 => => transferring context: 259B                                                                                          0.0s
 => CACHED [2/8] RUN apt-get update                                                                                        0.0s
 => CACHED [3/8] RUN apt-get upgrade -y                                                                                    0.0s
 => CACHED [4/8] RUN apt-get install -y python3 python3-pip                                                                0.0s
 => CACHED [5/8] RUN pip3 install flask                                                                                    0.0s
 => CACHED [6/8] RUN pip3 install flask-cors                                                                               0.0s
 => CACHED [7/8] WORKDIR /app                                                                                              0.0s
 => CACHED [8/8] COPY ./api.py /app/api.py                                                                                 0.0s
 => exporting to image                                                                                                     0.0s
 => => exporting layers                                                                                                    0.0s
 => => writing image sha256:839f2b832b76117f25a85adc73133026d69ab9008eb6c01459922b47cd61a2b1                               0.0s
 => => naming to docker.io/library/softy-pinko-back-end:task5                                                              0.0s
[+] Building 1.1s (8/8) FINISHED                                                                                                
 => [internal] load build definition from Dockerfile                                                                       0.0s
 => => transferring dockerfile: 188B                                                                                       0.0s
 => [internal] load .dockerignore                                                                                          0.0s
 => => transferring context: 2B                                                                                            0.0s
 => [internal] load metadata for docker.io/library/nginx:latest                                                            0.9s
 => [1/3] FROM docker.io/library/nginx:latest@sha256:593dac25b7733ffb7afe1a72649a43e574778bf025ad60514ef40f6b5d606247      0.0s
 => [internal] load build context                                                                                          0.0s
 => => transferring context: 2.68MB                                                                                        0.0s
 => CACHED [2/3] COPY ./softy-pinko-front-end /var/www/html/softy-pinko-front-end                                          0.0s
 => CACHED [3/3] COPY ./softy-pinko-front-end.conf  /etc/nginx/conf.d/default.conf                                         0.0s
 => exporting to image                                                                                                     0.0s
 => => exporting layers                                                                                                    0.0s
 => => writing image sha256:7c073606697aa4578af1ddad5f2210e611f5aa0762175006d0992b637c1de081                               0.0s
 => => naming to docker.io/library/softy-pinko-front-end:task5                                                             0.0s
[+] Building 0.4s (7/7) FINISHED                                                                                                
 => [internal] load build definition from Dockerfile                                                                       0.0s
 => => transferring dockerfile: 31B                                                                                        0.0s
 => [internal] load .dockerignore                                                                                          0.0s
 => => transferring context: 2B                                                                                            0.0s
 => [internal] load metadata for docker.io/library/nginx:latest                                                            0.3s
 => [internal] load build context                                                                                          0.0s
 => => transferring context: 32B                                                                                           0.0s
 => [1/2] FROM docker.io/library/nginx:latest@sha256:593dac25b7733ffb7afe1a72649a43e574778bf025ad60514ef40f6b5d606247      0.0s
 => CACHED [2/2] COPY ./proxy.conf /etc/nginx/conf.d/default.conf                                                          0.0s
 => exporting to image                                                                                                     0.0s
 => => exporting layers                                                                                                    0.0s
 => => writing image sha256:bc5dd1e5f2e15577273ef909c555c32b7dfd892c5c6f07f1d50a8519a4c99f32                               0.0s
 => => naming to docker.io/library/softy-pinko-proxy:task5                                                                 0.0s
Dereks-MacBook-Pro:task5 derekwebb$ docker-compose up
[+] Running 3/0
 ⠿ Container task5-back-end-1   Created                                                                                    0.0s
 ⠿ Container task5-front-end-1  Created                                                                                    0.0s
 ⠿ Container task5-proxy-1      Created                                                                                    0.0s
Attaching to task5-back-end-1, task5-front-end-1, task5-proxy-1
task5-back-end-1   |  * Serving Flask app 'api'
task5-back-end-1   |  * Debug mode: off
task5-back-end-1   | WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
task5-back-end-1   |  * Running on all addresses (0.0.0.0)
task5-back-end-1   |  * Running on http://127.0.0.1:5252
task5-back-end-1   |  * Running on http://172.19.0.2:5252
task5-back-end-1   | Press CTRL+C to quit
task5-front-end-1  | /docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
task5-front-end-1  | /docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
task5-front-end-1  | /docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
task5-front-end-1  | 10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
task5-front-end-1  | 10-listen-on-ipv6-by-default.sh: info: /etc/nginx/conf.d/default.conf differs from the packaged version
task5-front-end-1  | /docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
task5-front-end-1  | /docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
task5-front-end-1  | /docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
task5-front-end-1  | /docker-entrypoint.sh: Configuration complete; ready for start up
task5-front-end-1  | 2023/06/15 19:28:38 [notice] 1#1: using the "epoll" event method
task5-front-end-1  | 2023/06/15 19:28:38 [notice] 1#1: nginx/1.25.1
task5-front-end-1  | 2023/06/15 19:28:38 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14) 
task5-front-end-1  | 2023/06/15 19:28:38 [notice] 1#1: OS: Linux 5.15.49-linuxkit
task5-front-end-1  | 2023/06/15 19:28:38 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
task5-front-end-1  | 2023/06/15 19:28:38 [notice] 1#1: start worker processes
task5-front-end-1  | 2023/06/15 19:28:38 [notice] 1#1: start worker process 28
task5-front-end-1  | 2023/06/15 19:28:38 [notice] 1#1: start worker process 29
task5-front-end-1  | 2023/06/15 19:28:38 [notice] 1#1: start worker process 30
task5-front-end-1  | 2023/06/15 19:28:38 [notice] 1#1: start worker process 31
task5-front-end-1  | 2023/06/15 19:28:38 [notice] 1#1: start worker process 32
task5-front-end-1  | 2023/06/15 19:28:38 [notice] 1#1: start worker process 33
task5-front-end-1  | 2023/06/15 19:28:38 [notice] 1#1: start worker process 34
task5-front-end-1  | 2023/06/15 19:28:38 [notice] 1#1: start worker process 35
task5-proxy-1      | /docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
task5-proxy-1      | /docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
task5-proxy-1      | /docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
task5-proxy-1      | 10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
task5-proxy-1      | 10-listen-on-ipv6-by-default.sh: info: /etc/nginx/conf.d/default.conf differs from the packaged version
task5-proxy-1      | /docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
task5-proxy-1      | /docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
task5-proxy-1      | /docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
task5-proxy-1      | /docker-entrypoint.sh: Configuration complete; ready for start up
task5-proxy-1      | 2023/06/15 19:28:39 [notice] 1#1: using the "epoll" event method
task5-proxy-1      | 2023/06/15 19:28:39 [notice] 1#1: nginx/1.25.1
task5-proxy-1      | 2023/06/15 19:28:39 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14) 
task5-proxy-1      | 2023/06/15 19:28:39 [notice] 1#1: OS: Linux 5.15.49-linuxkit
task5-proxy-1      | 2023/06/15 19:28:39 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
task5-proxy-1      | 2023/06/15 19:28:39 [notice] 1#1: start worker processes
task5-proxy-1      | 2023/06/15 19:28:39 [notice] 1#1: start worker process 28
task5-proxy-1      | 2023/06/15 19:28:39 [notice] 1#1: start worker process 29
task5-proxy-1      | 2023/06/15 19:28:39 [notice] 1#1: start worker process 30
task5-proxy-1      | 2023/06/15 19:28:39 [notice] 1#1: start worker process 31
task5-proxy-1      | 2023/06/15 19:28:39 [notice] 1#1: start worker process 32
task5-proxy-1      | 2023/06/15 19:28:39 [notice] 1#1: start worker process 33
task5-proxy-1      | 2023/06/15 19:28:39 [notice] 1#1: start worker process 34
task5-proxy-1      | 2023/06/15 19:28:39 [notice] 1#1: start worker process 35
task5-back-end-1   | 172.19.0.4 - - [15/Jun/2023 19:28:48] "GET /api/hello HTTP/1.0" 200 -
task5-front-end-1  | 2023/06/15 19:28:49 [error] 30#30: *27 open() "/var/www/html/softy-pinko-front-end/favicon.ico" failed (2: No such file or directory), client: 172.19.0.4, server: softy-pinko-front-end, request: "GET /favicon.ico HTTP/1.0", host: "front-end:9000", referrer: "http://localhost/"
```

The following image shows the browser hitting <code>http://localhost:80</code>, which is the proxy server.

<strong>Browser</strong>

<img alt="" loading="lazy" src="https://drive.google.com/uc?id=1epLwZxracnyGUmS2P9EXHUheMI5-_eDh" style=""/>

Next, notice that you cannot access the front-end or back-end servers directly - you can only reach your services by going through the proxy server.

<strong>Browser</strong>

<img alt="" loading="lazy" src="https://drive.google.com/uc?id=1lU2rXbWR6lLD8-hf_sTz8gU2qBxXvPro" style=""/>

<img alt="" loading="lazy" src="https://drive.google.com/uc?id=13uL4OrNtbZaO23rGWF51OCTJFBDg4fV8" style=""/>

---

### 6. Scale Horizontally <a name='subparagraph6'></a>

Congratulations! You’ve got a proxy server that routes to either a static-content server or to an API server for dynamic data. This works well, for now…but what would happen if traffic to your site spiked up dramatically to where your API server becomes too flooded with requests to be able to respond quickly? In this case, we would like to add a second API server and load-balance all requests between those two servers. Luckily, with Nginx and Docker, this is a simple flag when running <code>docker-compose up</code>!

For this task, make a copy of the <code>task5</code> directory and name it <code>task6</code>. Your goal is to launch your Docker containers such that you have 2 (or more) API servers. Nginx will act as a load-balance between them so that every request goes to the next API server using the Round-Robin load-balancing algorithm.

Create a new file in the <code>task6</code> directory named <code>2-api-servers.txt</code> and put in the exact docker-compose command used to spin up two API servers, instead of just one. Note: for this task, the checker will assume that you named your back-end server <code>back-end</code> and your file must end with a newline Your output should look like this:

<strong>Terminal</strong>

```
[+] Running 4/0
 ⠿ Container task6-back-end-2   Created                                                                                    0.0s
 ⠿ Container task6-back-end-1   Created                                                                                    0.0s
 ⠿ Container task6-front-end-1  Created                                                                                    0.0s
 ⠿ Container task6-proxy-1      Created                                                                                    0.0s
Attaching to task6-back-end-1, task6-back-end-2, task6-front-end-1, task6-proxy-1
task6-back-end-2   |  * Serving Flask app 'api'
task6-back-end-2   |  * Debug mode: off
task6-back-end-2   | WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
task6-back-end-2   |  * Running on all addresses (0.0.0.0)
task6-back-end-2   |  * Running on http://127.0.0.1:5252
task6-back-end-2   |  * Running on http://172.20.0.2:5252
task6-back-end-2   | Press CTRL+C to quit
task6-back-end-1   |  * Serving Flask app 'api'
task6-back-end-1   |  * Debug mode: off
task6-back-end-1   | WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
task6-back-end-1   |  * Running on all addresses (0.0.0.0)
task6-back-end-1   |  * Running on http://127.0.0.1:5252
task6-back-end-1   |  * Running on http://172.20.0.3:5252
task6-back-end-1   | Press CTRL+C to quit
task6-front-end-1  | /docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
task6-front-end-1  | /docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
task6-front-end-1  | /docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
task6-front-end-1  | 10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
task6-front-end-1  | 10-listen-on-ipv6-by-default.sh: info: /etc/nginx/conf.d/default.conf differs from the packaged version
task6-front-end-1  | /docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
task6-front-end-1  | /docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
task6-front-end-1  | /docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
task6-front-end-1  | /docker-entrypoint.sh: Configuration complete; ready for start up
task6-front-end-1  | 2023/06/15 19:52:22 [notice] 1#1: using the "epoll" event method
task6-front-end-1  | 2023/06/15 19:52:22 [notice] 1#1: nginx/1.25.1
task6-front-end-1  | 2023/06/15 19:52:22 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14) 
task6-front-end-1  | 2023/06/15 19:52:22 [notice] 1#1: OS: Linux 5.15.49-linuxkit
task6-front-end-1  | 2023/06/15 19:52:22 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
task6-front-end-1  | 2023/06/15 19:52:22 [notice] 1#1: start worker processes
task6-front-end-1  | 2023/06/15 19:52:22 [notice] 1#1: start worker process 27
task6-front-end-1  | 2023/06/15 19:52:22 [notice] 1#1: start worker process 28
task6-front-end-1  | 2023/06/15 19:52:22 [notice] 1#1: start worker process 29
task6-front-end-1  | 2023/06/15 19:52:22 [notice] 1#1: start worker process 30
task6-front-end-1  | 2023/06/15 19:52:22 [notice] 1#1: start worker process 31
task6-front-end-1  | 2023/06/15 19:52:22 [notice] 1#1: start worker process 32
task6-front-end-1  | 2023/06/15 19:52:22 [notice] 1#1: start worker process 33
task6-front-end-1  | 2023/06/15 19:52:22 [notice] 1#1: start worker process 34
task6-proxy-1      | /docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
task6-proxy-1      | /docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
task6-proxy-1      | /docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
task6-proxy-1      | 10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
task6-proxy-1      | 10-listen-on-ipv6-by-default.sh: info: /etc/nginx/conf.d/default.conf differs from the packaged version
task6-proxy-1      | /docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
task6-proxy-1      | /docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
task6-proxy-1      | /docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
task6-proxy-1      | /docker-entrypoint.sh: Configuration complete; ready for start up
task6-proxy-1      | 2023/06/15 19:52:23 [notice] 1#1: using the "epoll" event method
task6-proxy-1      | 2023/06/15 19:52:23 [notice] 1#1: nginx/1.25.1
task6-proxy-1      | 2023/06/15 19:52:23 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14) 
task6-proxy-1      | 2023/06/15 19:52:23 [notice] 1#1: OS: Linux 5.15.49-linuxkit
task6-proxy-1      | 2023/06/15 19:52:23 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
task6-proxy-1      | 2023/06/15 19:52:23 [notice] 1#1: start worker processes
task6-proxy-1      | 2023/06/15 19:52:23 [notice] 1#1: start worker process 28
task6-proxy-1      | 2023/06/15 19:52:23 [notice] 1#1: start worker process 29
task6-proxy-1      | 2023/06/15 19:52:23 [notice] 1#1: start worker process 30
task6-proxy-1      | 2023/06/15 19:52:23 [notice] 1#1: start worker process 31
task6-proxy-1      | 2023/06/15 19:52:23 [notice] 1#1: start worker process 32
task6-proxy-1      | 2023/06/15 19:52:23 [notice] 1#1: start worker process 33
task6-proxy-1      | 2023/06/15 19:52:23 [notice] 1#1: start worker process 34
task6-proxy-1      | 2023/06/15 19:52:23 [notice] 1#1: start worker process 35
```

If you go to the web page and reload it several times, your terminal output should look like this:

<strong>Terminal</strong>

```
task6-back-end-1   | 172.20.0.5 - - [15/Jun/2023 19:53:55] "GET /api/hello HTTP/1.0" 200 -
task6-back-end-2   | 172.20.0.5 - - [15/Jun/2023 19:53:57] "GET /api/hello HTTP/1.0" 200 -
task6-back-end-1   | 172.20.0.5 - - [15/Jun/2023 19:53:58] "GET /api/hello HTTP/1.0" 200 -
task6-back-end-2   | 172.20.0.5 - - [15/Jun/2023 19:53:58] "GET /api/hello HTTP/1.0" 200 -
task6-back-end-1   | 172.20.0.5 - - [15/Jun/2023 19:53:59] "GET /api/hello HTTP/1.0" 200 -
task6-back-end-2   | 172.20.0.5 - - [15/Jun/2023 19:54:00] "GET /api/hello HTTP/1.0" 200 -
task6-back-end-1   | 172.20.0.5 - - [15/Jun/2023 19:54:00] "GET /api/hello HTTP/1.0" 200 -
task6-back-end-2   | 172.20.0.5 - - [15/Jun/2023 19:54:01] "GET /api/hello HTTP/1.0" 200 -
```

Note how the back-end server that the Nginx load-balancer routed to goes between the two different back-end servers. Here is an example with 5 back-end servers:

<strong>Terminal</strong>

```
task6-back-end-2   | 172.20.0.8 - - [15/Jun/2023 19:55:21] "GET /api/hello HTTP/1.0" 200 -
task6-back-end-5   | 172.20.0.8 - - [15/Jun/2023 19:55:22] "GET /api/hello HTTP/1.0" 200 -
task6-back-end-1   | 172.20.0.8 - - [15/Jun/2023 19:55:23] "GET /api/hello HTTP/1.0" 200 -
task6-back-end-4   | 172.20.0.8 - - [15/Jun/2023 19:55:23] "GET /api/hello HTTP/1.0" 200 -
task6-back-end-3   | 172.20.0.8 - - [15/Jun/2023 19:55:24] "GET /api/hello HTTP/1.0" 200 -
task6-back-end-2   | 172.20.0.8 - - [15/Jun/2023 19:55:24] "GET /api/hello HTTP/1.0" 200 -
task6-back-end-5   | 172.20.0.8 - - [15/Jun/2023 19:55:24] "GET /api/hello HTTP/1.0" 200 -
task6-back-end-1   | 172.20.0.8 - - [15/Jun/2023 19:55:25] "GET /api/hello HTTP/1.0" 200 -
task6-back-end-4   | 172.20.0.8 - - [15/Jun/2023 19:55:25] "GET /api/hello HTTP/1.0" 200 -
task6-back-end-3   | 172.20.0.8 - - [15/Jun/2023 19:55:26] "GET /api/hello HTTP/1.0" 200 -
task6-back-end-2   | 172.20.0.8 - - [15/Jun/2023 19:55:26] "GET /api/hello HTTP/1.0" 200 -
task6-back-end-5   | 172.20.0.8 - - [15/Jun/2023 19:55:27] "GET /api/hello HTTP/1.0" 200 -
task6-back-end-1   | 172.20.0.8 - - [15/Jun/2023 19:55:27] "GET /api/hello HTTP/1.0" 200 -
task6-back-end-4   | 172.20.0.8 - - [15/Jun/2023 19:55:27] "GET /api/hello HTTP/1.0" 200 -
task6-back-end-3   | 172.20.0.8 - - [15/Jun/2023 19:55:28] "GET /api/hello HTTP/1.0" 200 -
```

---


## Authors
vtiquet - [GitHub Profile](https://github.com/vtiquet)
