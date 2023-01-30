# Docker Practical Course - [Tresmerge](https://www.youtube.com/watch?v=tHP5IWfqPKk&list=PLzNfs-3kBUJnY7Cy1XovLaAkgfjim05RR&index=1)


### Table of content

1. [Docker desktop.](#desc0)
2. [Basic Dockerfile.](#desc1)
3. [Basic Command Lines.](#desc2)
4. [The difference between images and containers.](#desc3)
5. [Docker Optimization.](#desc4)
6. [Volumes.](#desc5)
7. [Hot Reload.](#desc6)
8. [Docker Compose.](#desc7)
9. [Environment Variables.](#desc8)
10. [Docker Environments Management.](#desc9)
11. [Multi-Stage Dockerfile.](#desc10)

<a name="desc0"></a>
## Docker desktop

- First of all, you have to install [Docker desktop](https://docs.docker.com/) on your machine.


<a name="desc1"></a>
## Basic Dockerfile
```
FROM node:14

WORKDIR /app

COPY package.json .

RUN npm install 

COPY . .

EXPOSE 4000

CMD [ "npm", "start" ]

```

#### 1. ```FROM node:14```
Inside the container, install node js.

#### 2. ```WORKDIR /app```
Create Folder "app".

#### 3. ```COPY package.json .```
copy package.json inside "app" Folder.

#### 4. ```RUN npm install ```
RUN nmp install to install all the packages inside package.json -> node_modules.

#### 5. ```COPY . . ```
Copy all files inside the container.

#### 6. ```EXPOSE 4000 ```
The app will run on port 4000, just for documentation.

#### 7. ```CMD [ "npm", "start" ] ```
Run the App using npm.

#### 8. ```CMD [ "npm", "run", "start-dev" ] ```
Run the App using nodemon, To make the server detect all the changes.

- Check out the figure for more explanation.

<img alt="Dockerfile" src="assets/Dockerfile.png" />

<a name="desc2"></a>
## Basic Command Lines

#### 1. ``` docker build -t "name of the Dockerfile" ```
- example:- ```docker build -t express-node-app .```
- After build the Dockerfile (you have an "image" now).
- Dockerfile → bulid → image → Run → Container.

#### 2. ``` docker run --name "name of the container" "name of image" ```
- example:- ```docker run --name express-node-app-container express-node-app```, 1st way.
- example:- ```docker run --name express-node-app-container -d express-node-app```, 2nd way, What is new here is the flag ```-d``` for detach the logs.

#### 3. ``` docker run --name "name of the container" "name of image" ```
- example:- ```docker run --name express-node-app-container express-node-app```, 1st way.
- example:- ```docker run --name express-node-app-container -d express-node-app```, 2nd way, What is new here is the flag ```-d``` for detach the logs.
- Now, if you run the app on your localhost, it doesn't work. You have to ***forward the port*** first. How?! , See the following command line.


#### 4. ```docker run --name "name of the container" -d -p my local machine port:container port "name of image"  ```
- example:- ```docker run --name express-node-app-container -d -p 4000:4000 express-node-app```

#### 5. ```docker image ls```
- list all images.

#### 6. ```docker image ls```
- list all the running containers.

#### 7. ```docker rm "the name of container" -f```
- example:- ```docker rm express-node-app-container -f```.

#### 8. ```docker exec -it "name of the container" bash```
- example:- ```docker exec -it express-node-app-container bash```.
- Open an interactive terminal in the container.
- Your terminal now:
``` 
root@dee98138027a:/app# ls
Dockerfile  index.js  node_modules  package-lock.json  package.json
root@dee98138027a:/app#
```
#### 9. ```docker logs "the name of container"```
- example:- ```docker logs  express-node-app-container```.
- To see the logs of the container.

#### 10. ```docker --help```
- For all command-line details.

<a name="desc3"></a>
## The difference between images and containers

- Check out the figure.

<img alt="ImagesVScontainers" src="assets/ImagesVScontainers.png" />

<a name="desc4"></a>
## Docker Optimization
- Create ```.dockerignore``` to avoid useless files.
- Why was the package.json file copied first and then all the other files copied again?
- Good question; check the figure for the answer.

<img alt="package" src="assets/package.png" />

<a name="desc5"></a>
## Volumes
- When do we need Docker Volumes?
- What exactly is Docker Volumes?
- Docker Volumes Types.
- Check out the figure.

<img alt="Docker Volumes" src="assets/Docker Volumes.png" />

<a name="desc6"></a>
## Hot Reload
- syncing between the local environment and the container.
- When you run the container, use that ```run --name "name of the container"``` ```-v``` ***absolute path for your local directory***:***Whatever directory is inside the container you want to sync with*** ```-d -p my local machine port:container port "name of image"```.
- example:- ```docker run --name express-node-app-container -v /home/mohamed/Desktop/Safrot/Projects/Docker/node-app:/app -d -p 4000:4000 express-node-app```.
- Also, you can use this instead of the full path:
    - Linux and mac: ```$(pwd)```, The command line will be as follows: ```docker run --name express-node-app-container -v $(pwd):/app -d -p 4000:4000 express-node-app```.
    - Windows: ```%cd%```, The command line will be as follows: ```docker run --name express-node-app-container -v %cd%:/app -d -p 4000:4000 express-node-app```.
- But what are the problems here?
    - If you add or delete files inside the container or on the local machine, this will change both sides at the same time.
    - One suggested solution is to sync or bind the application source code on your localhost with the application source code in the container,      considering that you make the directory in the container read-only.
    - example: 
        - ```docker run --name express-node-app-container -v $(pwd)/src:/app/src:ro -d -p 4000:4000 express-node-app```
 

<a name="desc7"></a>
## Docker Compose
- What is Docker Compose?
    - Docker Compose is a tool for defining and running multi-container Docker applications.by using a ```YAML file``` to configure your application services. 
    Then, with a single command, you create and start all the services in your configuration.
    
- ```docker-compose.yml:```
 ``` 
 version: "3"
services:
  node-app: 
    container_name: express-node-app-container
    build: .
    volumes:
      - ./src:/app/src:ro
    ports: 
      - "4000:4000"
```  
- Check out the figure.
<img alt="docker-compose" src="assets/Docker Compose.png" />


<a name="desc8"></a>
## Environment Variables.

- How to pass environment variables to the container?
    1. Via the Dockerfile itself, as follows:
        - You'll notice here that we used ```ENV PORT=4000``` inside the Docker file itself.
    ```
    FROM node:14

    WORKDIR /app

    COPY package.json .

    RUN npm install 

    COPY . .
    
    ENV PORT=4000

    EXPOSE $PORT

    CMD [ "npm", "run", "start-dev" ]

    ```
    2. Via the command line, as follows:
    ```
    --env list                       Set environment variables
    --env-file list                  Read in a file of environment variables
    ```
     - eg:
    ```
    - Set environment variables:
    docker run --name express-node-app-container -v $(pwd)/src:/app/src:ro --env PORT=400 --env NODE_ENV=development -d -p 4000:4000 express-node-app
    - Read in a file of environment variables:
    ocker run --name express-node-app-container -v $(pwd)/src:/app/src:ro --env-file ./.env -d -p 4000:4000 express-node-app
    ```
    - So, if you open an interactive terminal in the container and use printenv, you will find your env variables.
    ```
    $ docker exec -it express-node-app-container bash
    root@78ca00db2d62:/app# printenv
    PORT=400
    NODE_ENV=development
    ```
    3. Via the docker-compose, as follows:
      
     - Set environment variables.
    ```
    version: "3"
    services:
        node-app: 
         container_name: express-node-app-container
         build: .
         volumes:
          - ./src:/app/src:ro
         ports: 
          - "4000:4000"
         environment:
          - Port=4000
          - NODE_ENV=production
    ```
    - Read in a file of environment variables.
    
    
   ```
    version: "3"
    services:
        node-app: 
         container_name: express-node-app-container
         build: .
         volumes:
          - ./src:/app/src:ro
         ports: 
          - "4000:4000"
         env_file:
          - ./.env
    ```

<a name="desc9"></a>
## Docker Environments Management

- Docker environment management across multiple environments.
- The first method and the optimization.
- Check out the figure.
<img alt="Docker environment management" src="assets/Docker environment management.png" />

- the command lines in the figure, to be copied if you want
   - ```Run: docker-compose -f docker-compose.dev.yml up -d```
   - ```Run: docker-compose -f docker-compose.prod.yml up -d```
   - ```Development Run: sudo docker-compose -f docker-compose.yml  -f docker-compose.dev.yml  up -d```
   - ```Production Run: sudo docker-compose -f docker-compose.yml  -f docker-compose.prod.yml  up -d```
   - ```docker exec -it express-node-app-container bash```

<a name="desc10"></a>
## Multi-Stage Dockerfile

- The first solution is that you can create a Dockerfile for each environment.
- The second solution is that you can create a single Docker file and manage all environments through it.
    - in the last part we faced tow proplems 
        -  Nodemon is installed in node modules, although we are running the container from the production compose file and not from the development compose file.
        -  We have to run ```CMD [ "npm", "start" ]``` in production and ```CMD [ "npm", "run", "start-dev" ]``` in development.
