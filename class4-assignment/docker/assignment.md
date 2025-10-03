This assigment is for running docker-engine on my aws ec2-instance named as devops.layman  

For this assigment, we have created a dummy flask application in python using claude.
During local installation of this Flask app, I have installed the requirements and successfully ran "python app.py" locally.

#For installating multiple application, can we achiveved in single line by creating a requirment text file and mention the name of application with version number and can be run using "-r"
E.g. Here we have installed the python based Flask and Werkzeug using "pip install -r requirements.

Initially locally ran the Flask application.

Screenshot:

![alt text](image.png)

Later, created a Dockerfile on the same path where we mentioned using a base image as python(taken from docker hub) and wrote rest of the files with comments at every step.

Ran "docker login" Authenticates Credentials: It sends your username and password (or an access token) to the specified Docker registry to verify your identity.
Stores Credentials: If authentication is successful, docker login stores your credentials locally in a configuration file, typically located at ~/.docker/config.json. This allows subsequent Docker commands (like docker pull or docker push) to interact with the registry without requiring re-authentication.

Created a docker image named as "myfirst-dockerapp" using command "docker build -t myfirst-dockerapp"

![alt text](screenshots/image-1.png)

So in my dockerfile initially i have created my Dockerfile with curly braces and it gave me a warning and created the docker image.

**Rebuilding a Docker image with the same name and tag effectively "overwrites" the previous image with that tag. Docker does not truly delete the old image immediately but rather untags it, leaving it as a "dangling" image (tagged as <none>). The newly built image then takes on the specified name and tag.

Later I have ran the build command again with same name and it created a new image & removed the name of last image. Then I have deleted the docker old image using imaged ID.

So best practice is to always mention a tag

Screenshot attached for reference:
![alt text](screenshots/image-2.png)

Started the container and mentioned it to run till die

docker run -td "imagename"

![alt text](image-3.png)

Logged into a container using interactive bash shell

"docker exec -it "ïmageid" bash

![alt text](image-4.png)

To access the application outside of container, ran below command stating port of container on which application is running and the port for local vm. Able to open the flask on browser

docker run -td -p 5000:5000 myfirst-dockerapp:latest

![alt text](image-5.png)

** We don't use docker hub in production environment. We use ECR repositories for security purposes in cloud. Even in docker hub as well we do not use public images

We have created a docker repository

![alt text](image-6.png)

Created a new image for docker reporsitories. 

docker tag myfirst-dockerapp:latest shubvrm/devops.layman:3rdOct

![alt text](image-8.png)

Did docker push. However received token authentication error. Checked docker hub and my token was expired. Hence, created a new token and logged in with commands shared by docker & pushed the image successfully.

![alt text](image-7.png)
![alt text](image-9.png)
![alt text](image-10.png)

Now tried running a container with docker image(pushed before). However, getting the error for port binding. I checked and one of container is already running with same port and i m suspecting that is the reason it says port is also allocated

![alt text](image-11.png)

Hence, i stopped the previous container and run the container with docker image.

"docker run -td -p 5000:5000 shubvrm/devops.layman:3rdOCt"

![alt text](image-12.png)

So I logged into my current container made with Dockerfile and there as per COPY command i have copied everything from my local directory to working directory i.e /py-app

![alt text](image-13.png)

Created a new file Dockerfile.better as the same path and build the new image with docker repo name and tag as better-image & created a new image of size 138MB

docker build -t shubvrm/devops.layman:better-image -f Dockerfile.better .

![alt text](image-14.png)

Pushed the new image to docker repositories

docker push shubvrm/devops.layman:better-image

![alt text](image-15.png)

![alt text](image-16.png)

