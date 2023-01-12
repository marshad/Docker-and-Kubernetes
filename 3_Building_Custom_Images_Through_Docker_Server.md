## 29: Creating Docker Images

* `hello-world` image
* `redis` image
* `busybox` image

Steps: Docker file --> Docker client --> Docker Server --> Usable Image!

Creating a Dockerfile Flow:

(a) Specify a base image
(b) Run some commands to install additional programs
(c) Specify a command to run on container startup

## 30: Buildkit for Docker Desktop

## 31: Building a Dockerfile

Goal: Create an image that runs a redis-server

1. $ mkdir redis-image
2. $ cd redis-image
3. touch Dockerfile 				# Create a file named 'Dockerfile' containing the following

```
# Use an existing docker image as a "base"
FROM alpine

# Download and install a dependency
RUN apk add --update redis 			# apk is a program called "APACHE PACKAGE MANAGER" that 
					   	# comes pre-installed with alpine

# Tell the image what to do when it starts as a container
CMD ["redis-server"]
```

4. $ docker build . 				# Successfully built IMAGE_ID
5. $ docker run IMAGE_ID 			# Ready to accept connections

## 32: Dockerfile Teardown

FROM alpine					# Use an existing docker image as a base

RUN apk add --update redis 			# Download and install a dependency

CMD ["redis-server"] 				# Tell the image what to do when it starts as a container

## 33: What's a Base Image

Writing a Dockerfile == Being given a computer with no OS and being told to install Google Chrome

How do you install Chrome on a computer with no operationg system?

(a) specify a base image (FROM)
	1. Install an OS

(b) run commands to install additional programs (RUN)
	2. Start up your default browser
	3. Navigate to chrome.google.com
	4. Download installer
	5. Open file/folder explorer
	6. Execute chrome_installer.exe

(c) command to run on startup (CMD)
	7. Execute chrome.exe


## 34: The Build Process in Detail

$ docker build . 				# Giving our 'Dockerfile' to docker CLI 
						# '.' specifies the build-context
						# The set of files/folder that we want in a docker image
FROM alpine

latest: Pulling from library/apline
Digest: ...
Status: Downloaded newer image for alpine:latest

Lecture #35: A Brief Recap

Lecture #36: Rebuilds with Cache

# Use an existing docker image as a base
FROM alpine

# Download and install a dependency
RUN apk add --update redis 					# apk is a program called apache package manager that comes pre-installed with alpine
RUN apk add --update gcc

# Tell the image what to do when it starts as a container
CMD ["redis-server"]

## 37: Tagging an Image

$ docker build -t <docker-id>/redis:latest . 			# '.' is the build context

Lecture #39: Manual Image Generation with Docker Commit

1. $ docker run -it alpine sh 					# -i: attach our terminal to the STDIN channel of the new running process
								# -t: show text in readable form 

2. $ apk add --update redis 					# Inside the container manually install redis

3. $ docker ps 							# Get CONTAINER_ID

4. $ docker commit -c 'CMD ["redis-server"]' CONTAINER_ID 	# -CONTAINER_ID

5. $ docker image ls -a

6. $ docker run IMAGE_ID