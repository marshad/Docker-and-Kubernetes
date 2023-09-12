* The GOAL of this porject is:
  - CREATE a tiny `Node JS WebApp`
  - WRAP it inside a Docker Container, and 
  - be able to ACCESS it from a browser running on a local-machine

## 37: Project Outline

* Steps:
  - 1. Crete a `Node JS WebApp`
  - 2. Create a Dockerfile
  - 3. Build image from Dockerfile
  - 4. Run image as container
  - 5. Connect to WebApp from a browser

## 38: Node Server Setup

```
$ mkdir simpleweb
$ cd simpleweb
```

* (a) create a file named `package.json` with the following content:
```
{
  "dependencies": {
    "express": "*"
  },
  "scripts": {
    "start": "node index.js"
  }
}
```

* (b) crate a file named `index.js` with the following content:
```
const express = require('express');

const app = express();
 
app.get('/', (req, res) => {
  res.send('How are you doing');
});
 
app.listen(8080, () => {
  console.log('Listening on port 8080');
});
```

## 39: A Few Planned Errors

* Create a file named `Dockerfile`

```
# Specify a base image
FROM alpine

# Install some dependencies
RUN npm install

# Default command
CMD ["npm", "start"]
```

```
$ docker build . 				# '.' is the build context
```

* We should see the following: 
  - `/bin/sh: npm` not found
  - The command `/bin/sh -c npm install` returned a non-zero code: 127

## 40: Base Image Issues

* Update Dockerfile file

```
# Specify a base image
FROM node:14-alpine 				# Notice: We're now using node:alpine and not alpine

# Install some dependencies
RUN npm install 				# Install deps by running 'npm install'. Node Package Manager (npm)

# Default command
CMD ["npm", "start"] 				# Start server by running 'npm start'
```

```
$ docker build .
```

## 41: A Few Missing Files

```
$ docker build .
```

* npm WARN saveError ENOENT: no such file or directory, open '/package.json'
* npm notice created a lockfile as package-lock.json. You should commit this file.
* npm WARN enoent ENOENT: no such file or directory, open '/package.json'
* npm WARN !invalid#2 No description
* npm WARN !invalid#2 No repository field.
* npm WARN !invalid#2 No README data
* npm WARN !invalid#2 No license field.

## 42: Copying Build Files

* Update Dockerfile file

```
# Specify a base image
FROM node:14-alpine

# Install some dependencies
COPY ./ ./ 						# COPY (current working directory of simpleweb) 
							#      (current working directory inside the container)
RUN npm install

# Default command
CMD ["npm", "start"]
```

```
$ docker build . 
```

* The following is fine. No issue:
  - npm notice created a lockfile as package-lock.json. You should commit this file.
  - npm WARN !invalid#2 No description
  - npm WARN !invalid#2 No repository field.
  - npm WARN !invalid#2 No license field.
  - Successfully built IMAGE_ID

```
$ docker build -t marshad1/simpleweb .			# Successfully tagged marshad1/simpleweb:latest
							# <docker-id>/<project-name>:latest
```

```
$ docker run marshad1/simpleweb
>> Listening on port 8080
```

`localhost:8080` 						# The sige can't be reached

## 43: Container Port Mapping at RUNTIME

```
$ docker build -t marshad1/simpleweb .			# Tagging an image <docker-id>/<project-id>
$ docker image ls -a
$ docker run marshad1/simpleweb				# https://localhost:8080, nothing prints out!
$ ctrl-c
```

```
$ docker run -p (Route incoming requests to this port on local-host to...):(...this port inside the container) IMAGE_ID
$ docker run -p 8080:8080 marshad1/simpleweb		# Port mapping
$ docker run -it marshad1/simpleweb sh
```

## 44: Specifying Working Directory

Update Dockerfile file

```
# Specify a base image
FROM node:14-alpine

WORKDIR /usr/app

# Install some dependencies
COPY ./ ./ 							# COPY (current working directory of simpleweb) (current working directory inside the container)

RUN npm install

# Default command
CMD ["npm", "start"]
```

```
$ docker run -p (Route incoming requests to this port on local host to...):(...this port inside the container) IMAGE_ID
$ docker run -p 8080:8080 marshad1/simpleweb

# Run another process inside a docker container
$ docker exec -it CONTAINER_ID sh
```

## 44: Specifying a Working Directory

```
$ docker run -it marshad1/simpleweb sh
/ # ls
/ # exit

WORKDIR /usr/app 					# Any following command will be executed relative 
							# to this path in the container
```

```
# Specify a base image
FROM node:14-alpine 					# Notice node:14- apline

WORKDIR /usr/app

# Install some dependencies
COPY ./ ./
RUN npm install

# Default command
CMD ["npm", "start"]
```

```
$ docker build -t marshad1/simpleweb .				# tag the image
$ docker run -p 8080:8080 marshad1/simpleweb			# docker run -p <port>:<port> dockerId/projectName
```

Start another process inside a running container 'docker exec'

```
$ docker ps 							# Get CONTAINER_ID
$ docker exec -it CONTAINER_ID sh
/usr/app #
```

## 45. Unnecessary Rebuilds

```
$ docker build -t marshad1/simpleweb . 				# '.' specify the build context
```

## 46: Minimizing Cache Busting and Rebuilds

Update `Dockerfile`

```
# Specify a base image
FROM node:14-alpine

WORKDIR /usr/app

# Install some depenendencies
COPY ./package.json ./

RUN npm install
COPY ./ ./

# Default command
CMD ["npm", "start"]
```

```
$ docker build -d marshad1/simpleweb .
```