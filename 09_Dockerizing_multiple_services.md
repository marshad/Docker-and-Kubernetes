## 116: Checkpoint Files

***

## 117: Checkpoint Catchup

***

## 118: Expected Output for Latest Create React App Versions

***

## 119: Dockerizing a React App - Again!

* We need to make `Dockerfile.dev` for each:
  - React App
  - Express Server API
  - Worker

```
$ cd complex/client
```

Create a file `Dockerfile.dev`

```
FROM node:16-alpine
WORKDIR '/app'
COPY ./package.json ./
RUN npm install
COPY . .
CMD ["npm", "run", "start"]
```

```
$ docker build -f Dockerfile.dev .          # '.' is build context
$ docker run CONTAINER_ID
```

***

## 120: Dockerizing Generic Node Apps

```
$ cd complex/server
```

Create a file `Docker.dev` 

```
FROM node:14.14.0-alpine
WORKDIR '/app'
COPY ./package.json ./
RUN npm install
COPY . .
CMD ["npm", "run", "dev"]           # Note `dev`
```

```
$ docker build -f Dockerfile.dev .
$ docker run CONTAINER_ID
```

```
$ cd complex/worker
```

Create a file `Docker.dev`

```
FROM node:alpine
WORKDIR '/app'
COPY ./package.json ./
RUN npm install
COPY . .
CMD ["npm", "run", "dev"]
```

```
$ docker build -f Dockerfile.dev .
$ docker run CONTAINER_ID
```

***

## 121: Postgres Database Required Fixed and Updates

***

## 122: Adding `Postgres` as a Service

Create a file `docker-compose.yml`

```
version: '3'
services:
  postgres:
    image: 'postgres:latest'
```

`http://hub.docker.com` --> Explore --> `postgres`

```
$ cd complex                    # `docker-compose.yml` file is present here
$ docker-compose up
```

***

## 123: Docker-compose Config

postgres
  What image to use?
redis
  What image to use?
server
  - Specify build
  - Specify volumes
  - Specify env variables

```
version: '3'
services:
  postgres:
    image: 'postgres:latest'
  redis:
    image: 'redis:latest'
  server:
    build:
      dockerfile: Dockerfile.dev
      context: ./server                     # look into this path or folder
    volumes:
      - /app/node_modules
      - ./server:/app                       # look at the server directory and copy everything there into the app folder in the container
```

***

## 127: Postgres Database is uninitialized or getaddrinfo NOTFOUND Fix

***

## 128: Environment Variables with Docker Compose

```
version: '3'
services:
  postgres:
    image: 'postgres:latest'
  redis:
    image: 'redis:latest'
  server:
    build:
      dockerfile: Dockerfile.dev
      context: ./server
    volumes:
      - /app/node_modules
      - ./server:/app                       # <src: server folder on local>:<dst: app folder on docker>
    environment:
      - REDIS_HOST=redis
      - REDIS_PORT=6379
      - PGUSER=postgres                     # default user
      - PGHOST=postgres                     # default host
      - PGDATABASE=postgres                 # default database
      - PGPASSWORD=postgres_password        # The default password for `postgres` is `postgres_password`
      - PGPORT=5432
```

```
$ docker-compose up
```

***

## 129: The Worker and Client Services

```
version: '3'
services:
  postgres:                             # service
    image: 'postgres:latest'
  redis:                                # service
    image: 'redis:latest'
  server:                               # service
    build:
      dockerfile: Dockerfile.dev
      context: ./server
    volumes:
      - /app/node_modules
      - ./server:/app
    environment:
      - REDIS_HOST=redis
      - REDIS_PORT=6379
      - PGUSER=postgres
      - PGHOST=postgres
      - PGDATABASE=postgres
      - PGPASSWORD=postgres_password
      - PGPORT=5432
  worker:                               # service
    build:
      dockerfile: Dockerfile.dev
      context: ./worker
    volumes:
      - /app/node_modules
      - ./worker:/app
  client:                               # service
    build:
      dockerfile: Dockerfile.dev
      context: ./client
    volumes:
      - /app/node_modules
      - ./client:/app                   # <src: client directory>:<dst: app folder inside container>
```

***

## 127: `Nginx` Path Routing

*Two Servers:
  - ReactServer
  - ExpressServer

Files:
  - `/index.html`--------> React Server
                   |
  - `main.js`---------

  - `/values/all` ---------> Express Server
                     |
  - `/vales/current` ---


* `Nginx`
  - What's the `req` start with?          # Looks at request path
  - `/api/`                               # Route to Express Server
  - `/`                                   # Route to React Server

***

## 128: Routing with `Nginx`

```
$ mkdir nginx
$ touch default.conf
```

* Tell Nginx that there is an 'upstream' server at client:3000
* Tell Nginx that there is an 'upstream' server at server:5000     
* Listen on port 80
* If anyone comes to '/' send them to client upstream
* If anyone comes to '/api' send them to server upstream

```
upstream client {
    server client:3000;
}

upstream api {
    server api:5000;
}

server {
    listen 80;                          # Nginx server listen on port 80

    location / {                        # Routing Rule
        proxy_pass http://client;       # Name of upstream 'client' above
    }

    location /api {                     # Routing Rule
        rewrite /api/(.*) /$1 break;    # $1 is ref to whatever is matched by (.*) regex
        proxy_pass http://api;          # Name of upstream server 'api' above
    }
}
```

***

## 129: Building a Custom `Nginx` Image

```
$ cd nginx
$ touch Dockerfile.dev 
```

```
FROM nginx
COPY ./default.conf /etc/nginx/conf.d/default.conf
```

Now add `nginx` as a service to our `docker-compose.yml` file

```
version: '3'
services:
  postgres:
    image: 'postgres:latest'
  redis:
    image: 'redis:latest'
  nginx:
    restart: always
    build:
      dockerfile: Dockerfile.dev
      context: ./nginx                  # Context is nginx directory
    ports:
      - '3050:80'                       # maps port 3050 on local machine to port 80 on container
  api:
    build:
      dockerfile: Dockerfile.dev
      context: ./server
    volumes:
      - /app/node_modules
      - ./server:/app
    environment:
      - REDIS_HOST=redis
      - REDIS_PORT=6379
      - PGUSER=postgres
      - PGHOST=postgres
      - PGDATABASE=postgres
      - PGPASSWORD=postgres_password
      - PGPORT=5432
  worker:
    build:
      dockerfile: Dockerfile.dev
      context: ./worker
    volumes:
      - /app/node_modules
      - ./worker:/app
  client:
    build:
      dockerfile: Dockerfile.dev
      context: ./client
    volumes:
      - /app/node_modules
      - ./client:/app
```
***


```
$ cd complex
$ docker-compose up --build                 # Force a build of everything
```

***

## 130: Starting Up Docker Compose

```
$ docker-compose up --build
```

***

## 131: Nginx connect() failed - Connection refused while connectin...

***

## 132: Troubleshooting Startup Bugs

***

## 133: WebSocket connection t `ws://localhost:3000/ws` failed

```
$ docker-compose down
$ docker-compose up --build
```

***

## 134: Opening Websocket Connections

```
upstream client {
    server client:3000;
}

upstream api {
    server api:5000;
}

server {
    listen 80;

    location / {
        proxy_pass http://client;
    }

    location /sockjs-node {
        proxy_pass http://client;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection Upgrade;
    }

    location /api {
        rewrite /api/(.*) /$1 break;
        proxy_pass http://api;
    }
}
```
