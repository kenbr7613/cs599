# Docker Example 
In this tutorial we will go over a basic docker example to help get new users started.
## Contents
1. Launch an EC2 Instance
2. Install Docker
3. Build your first image
4. Run and Test
5. Publish

## Launch an EC2 Instance
Start by launching an EC2 instance through the AWS Console. This step isn't necessary as you can install Docker on any widely available operating system, but for the purpose of this walk through it gives us a common environment to start from.
Make sure you open port 22 in your instances security group and have access to the private key associated with the instance so we can ssh into the machine.

    $ ssh -i cs599.pem ec2-user@ec2-18-212-200-166.compute-1.amazonaws.com

## Install Docker
Run the following commands to set up your environment with Docker installed and running. You also need to add your user to the Docker user group. You must logout and log back in for this user change to take effect.

    $ sudo yum update -y
    $ sudo amazon-linux-extras install docker
    $ sudo service docker start
    $ sudo usermod -a -G docker ec2-user
    $ logout
    $ ssh -i cs599.pem ec2-user@ec2-18-212-200-166.compute-1.amazonaws.com

## Build your first image
Create the following two files
index.html

    <h1>Sample File</h1>
Dockerfile

    FROM centos:7
    MAINTAINER Ken
    RUN yum -y install httpd
    COPY index.html /var/www/html/
    CMD ["/usr/sbin/httpd", "-D", "FOREGROUND"]
    EXPOSE 80
Make sure the file names are exactly as stated `index.html` and `Dockerfile` .
Once you have these files created, use Docker to create an image based on the specifications in the Dockerfile. 

    $ docker build -t test:v1 .
    
## Run and Test
Use the tag name from the previous step as the reference to launch the image. For this example we also want to specify an open port.

    $ docker run -dit -p 8000:80 test:v1

With the image running, we want to test access. Since this image is running a web server with an exposed port we can test with

    $ curl localhost:8000

If you have any issues, you can see the currently running processes with `docker ps` and check the logs for a process with `docker logs <process hash>`.

## Publish
Once you have an image you may want to share it. One of the easiest ways to do this is with a Docker hub account. You can create an account here
https://hub.docker.com/
Once you have an account, you want to log in locally, tag your image, and push it to the repository.

    $ docker login
    $ docker tag test:v1 username/dockerhub:test
    $ docker push username/dockerhub:test

You should then be able to see the image from Docker Hub, and others can reference it's tag name to use it themselves.

