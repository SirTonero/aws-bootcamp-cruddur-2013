# Week 1 — App Containerization

This week was all about containers and its used cases.

### What is a Container?

A container is a standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another. 

### What is an IMAGE?
executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries and settings.

Container images become containers at runtime and in the case of Docker containers – images become containers when they run on Docker Engine. Available for both Linux and Windows-based applications, containerized software will always run the same, regardless of the infrastructure. Containers isolate software from its environment and ensure that it works uniformly despite differences for instance between development and staging.


## Technical Task for Week 1 - Containerization

- ## create a github Repo
 The first tasked was to create a github repo of the AWS-Bootcamp-cruddur-2023 repo which container
 the backend and front apps folder that we would be working with.
 ![cruddur app repo](https://user-images.githubusercontent.com/112965272/220357778-c446591c-fc61-4d16-8a84-aa00c6b126ec.png)
 
 clicking the git pod button quickly took us to the gitpod envirnment, the environment began by running the `gitpod.yml` file which contain all th build configuration for the enviroment .
 
 - i updated my `gitpod.yml file` to get my environment ready with all the prerequisite installations
 
 find yaml below👇
 
 ```yaml
 tasks:
  - name: aws-cli
    env:
      AWS_CLI_AUTO_PROMPT: on-partial
    init: |
      cd /workspace
      curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
      unzip awscliv2.zip
      sudo ./aws/install
      cd $THEIA_WORKSPACE_ROOT
  - name: postgress
    init: |
      curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc|sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/postgresql.gpg
      echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" |sudo tee  /etc/apt/sources.list.d/pgdg.list
      sudo apt update
      sudo apt install -y postgresql-client-13 libpq-dev
  - name: initialise Frontend and Backend
    init: |
      cd /workspace/aws-bootcamp-cruddur-2023/backend-flask
      pip3 install -r requirements.txt
      export BACKEND_URL="*"
      export FRONTEND_URL="*"
      cd /workspace/aws-bootcamp-cruddur-2023/frontend-react-js
      npm i
      cd /workspace/aws-bootcamp-cruddur-2023/frontend-react-js
vscode:
  extensions:
    - 42Crunch.vscode-openapi
    - cweijan.vscode-postgresql-client2
```
 
 -------
 
 ## Containerized Backend Flask APP
 
 ### Run python
 -  i change directory into the backend-flask folder
 -  exported the FRONTEND_URL and BACKEND_URL into our machine  ` The effect of not doing this means the backend wont connect to the front end `
 -  install python3 with flask and set a host and a port to listen on .
 
 ```sh
 cd backend-flask
export FRONTEND_URL="*"
export BACKEND_URL="*"
python3 -m flask run --host=0.0.0.0 --port=4567
cd ..
```

### Output

![SCR-20230221-l7p](https://user-images.githubusercontent.com/112965272/220369752-f9e3d50d-16ee-4341-9920-7fdf4b704e6c.png)


- After running the above command we make sure we unlock the port in the port tab.👇 <br>

![Opening ports on vscode](https://user-images.githubusercontent.com/112965272/220370600-dfadcdbb-daea-44ce-85d7-26d9dbc9545d.png)

- open the link with our specified port of `4567` in my browser
- append(to add to the end ) the url to `/api/activities/home`
- and voila we got back a json page with data 
<br>

![SCR-20230221-ln0](https://user-images.githubusercontent.com/112965272/220374352-24d6150c-cca7-48ee-a9db-c2470cf22813.png)

## Containerize our Backend-flask app

- cd into the `backend-flask`
- touch `dockerfile ` (no file extension is needed for a docker file)
- or create the dockerfile here like this in the vscode explorer pane: `backend-flask/Dockerfile`

```dockerfile
 # Pulling this image from docker hub useing the FROM Command 
FROM python:3.10-slim-buster

# Inside Container
# MAke a new container inside container
WORKDIR /backend-flask

# Outside Container -> inside Container
# this containers the library we want to install to run the app
COPY requirements.txt requirements.txt

# Inside Container
#install the python libraries used for the app
RUN pip3 install -r requirements.txt

# Outside Container -> inside Container
# . means everything the current directory
# the first . - /backend-flask (outside container)
# the scond period ./backend-lask (inside container)
COPY . .

# Expose a port to listen to
EXPOSE ${PORT}

# CMD (command)
# python3 -m flask run --host=0.0.0.0 --port=4567
CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567"]

```

## Build container 

```sh
docker build -t  backend-flask ./backend-flask
```

## Run Container 

```sh
docker run --rm -p 4567:4567 -it backend-flask
FRONTEND_URL="*" BACKEND_URL="*" docker run --rm -p 4567:4567 -it backend-flask
export FRONTEND_URL="*"
export BACKEND_URL="*"
docker run --rm -p 4567:4567 -it -e FRONTEND_URL='*' -e BACKEND_URL='*' backend-flask
docker run --rm -p 4567:4567 -it  -e FRONTEND_URL -e BACKEND_URL backend-flask
unset FRONTEND_URL="*"
unset BACKEND_URL="*"
```

## Run the container in detach mode passing the `-d` flag

```sh
 docker container run --rm -p 4567:4567 -d backend-flask
```

## Get Container Images or Running Container Ids

```sh
docker images
docker ps
```
## Get all containers bot running and stopped

```sh
docker ps -a
```

## Check Container Logs
```sh
docker logs <container id> -f

docker logs backend-flask -f

## Check container Port

```sh
docker port <container id>
```

## passing a command into a running container

```sh
docker exec -it <container id> < example command > ps -aux
```

## Gaining shell access into a container 

```sh

docker exec -it <container id> sh 
```

## Delete a container

```sh
docker rm <container id>
```

## Deleting an image
```sh
docker rmi <image-name>
docker rmi backend-flask

```
 ## Containerized Frontend
 
 
 ## RUn NPM install
 NPM Install is required before building the container because it needs to copy the node_modules folder
 
 ```sh
 cd frontend-react-js
 npm i
 ```
 
 ### Create a Dockerfile
 
 create a dockerfile in the `frontend-react-js` folder
 `frontend-react-js/Dockerfile`
 
 i inserted the following instructions into my dockerfile
 
 ```dockerfile
# This command pull an image from registry with nodejs 
FROM node:16.18

# here we are setting the PORT for the APP
ENV PORT=3000

# we are copying the contents of the frontend-react-js into a created folder in the container call frontend-react-js
COPY . /frontend-react-js

# change the working directory to /frontend-react-js
WORKDIR /frontend-react-js

# The RUN Command install npm on the machine
RUN npm install

# exposes a port
EXPOSE ${PORT}

## cmd command here starts the NPM service
CMD ["npm", "start"]

```
 ## Build Container
 
 ```sh
 docker build -t frontend-react-js ./frontend-react-js
 ```
 
 ## Run Container
 
 ```sh
 docker run -p 3000:3000 -d frontend-react-js
 ```
 

- i made sure i opened the port using the steps found in the backend deployment 

![SCR-20230221-ors](https://user-images.githubusercontent.com/112965272/220409072-ac0d3e06-68ad-4b8f-bd5b-d3dcf4d3201b.png)


### We have our frontend-react-js App running (without data)
![SCR-20230221-oyr](https://user-images.githubusercontent.com/112965272/220412531-e6cfe682-0728-4856-bf84-523fbbd2b520.png)


## Multiple containers 

Docker Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a YAML file to configure your application’s services. Then, with a single command, you create and start all the services from your configuration. 

## Create a docker-compose file 
### create a `docker-compose.yml` at the root directory of my project `/aws-bootcamp-cruddur-2023`

find below the docker-compose.yaml file which buillds our back end and frontend app together so they can connect .

```yaml

version: "3.8"
services:
  backend-flask:
    environment:
      FRONTEND_URL: "https://3000-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
      BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./backend-flask
    ports:
      - "4567:4567"
    volumes:
      - ./backend-flask:/backend-flask
  frontend-react-js:
    environment:
      REACT_APP_BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./frontend-react-js
    ports:
      - "3000:3000"
    volumes:
      - ./frontend-react-js:/frontend-react-js

# the name flag is a hack to change the default prepend folder
# name when outputting the image names
networks: 
  internal-network:
    driver: bridge
    name: cruddur
```

## Running the docker-compose.yml file

Enter the command from From your project directory, start up your application by running

```sh

docker compose up

```

## Adding Postgres and DynamoDB Local

These are DB we would be using in a layer class hence we should install them now and add them to our docker compuse file


In future labs, we will use Postgres and DynamoDB locally. They can be brought in as containers and externally referenced.

In our existing Docker compose file, let's add the following:

### Postgres

```yaml

services:
  db:
    image: postgres:13-alpine
    restart: always
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    ports:
      - '5432:5432'
    volumes: 
      - db:/var/lib/postgresql/data
volumes:
  db:
    driver: local
```

To install the postgres client into Gitpod

```sh
  - name: postgres
    init: |
      curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc|sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/postgresql.gpg
      echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" |sudo tee  /etc/apt/sources.list.d/pgdg.list
      sudo apt update
      sudo apt install -y postgresql-client-13 libpq-dev
```



### DynamoDB Local

```yaml
services:
  dynamodb-local:
    # https://stackoverflow.com/questions/67533058/persist-local-dynamodb-data-in-volumes-lack-permission-unable-to-open-databa
    # We needed to add user:root to get this working.
    user: root
    command: "-jar DynamoDBLocal.jar -sharedDb -dbPath ./data"
    image: "amazon/dynamodb-local:latest"
    container_name: dynamodb-local
    ports:
      - "8000:8000"
    volumes:
      - "./docker/dynamodb:/home/dynamodblocal/data"
    working_dir: /home/dynamodblocal
    
```

## Volumes

directory volume mapping

```yaml
volumes: 
- "./docker/dynamodb:/home/dynamodblocal/data"
```

named volume mapping

```yaml
volumes: 
  - db:/var/lib/postgresql/data

volumes:
  db:
    driver: local
```



# HomeWork CHallenge

- Run the dockerfile CMD as an external script
- Push and tag a image to DockerHub (they have a free tier)
- Use multi-stage building for a Dockerfile build
- Implement a healthcheck in the V3 Docker compose file
- Research best practices of Dockerfiles and attempt to implement it in your Dockerfile
- Learn how to install Docker on your localmachine and get the same containers running outside of Gitpod / Codespaces
- Launch an EC2 instance that has docker installed, and pull a container to demonstrate you can run your own docker processes. 


 ## Push and tag an image to docker HUb
 
 - i went to [docker hub website](https://hub.docker.com/)
 -  Created a free-tier account and verified my email addresss as well as chose a unique USername `Mrtonero`
 -  i created a repositiory to store my images
 ![SCR-20230221-wsd](https://user-images.githubusercontent.com/112965272/220474590-3ee77c47-12ff-4203-adbb-a279791a3795.png)
 
 [my docker repositiory](https://hub.docker.com/repository/docker/mrtonero007/aws-bootcamp-cruddur-2023/general)
 
 - head over to my terminal and  typed
 ```sh
 docker login
 ````
 <img width="966" alt="SCR-20230221-x6e" src="https://user-images.githubusercontent.com/112965272/220477493-e71ccd89-b987-422d-8d99-a7bc8d261d6c.png">
 
 - i build the docker image to be pushed to my docker hub repository.
<img width="960" alt="SCR-20230221-x34" src="https://user-images.githubusercontent.com/112965272/220477782-49ebb996-22d3-4a5e-8b9e-e3ebfed85f35.png">

- then i tagged my image using the following commend 
```sh
docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]

docker tag backend-flask:latest mrtonero007/aws-bootcamp-cruddur-2023:week.
```

<img width="1033" alt="SCR-20230222-o9" src="https://user-images.githubusercontent.com/112965272/220482308-1cefaed0-4c41-427d-9f67-cf6d7c5b5053.png">

- i pushed the image using the command below
```sh

docker push [OPTIONS] NAME[:TAG]

push mrtonero007/aws-bootcamp-cruddur-2023:week.1
```

<img width="1027" alt="SCR-20230222-10u" src="https://user-images.githubusercontent.com/112965272/220485469-dee50263-8a5f-4e15-adb0-583ae230cdd9.png">
      
![SCR-20230222-zx](https://user-images.githubusercontent.com/112965272/220485545-e1efee1a-331e-4b55-b779-e521374e6937.png)

👇

[Link to my image in docker hub](https://hub.docker.com/layers/mrtonero007/aws-bootcamp-cruddur-2023/week.1/images/sha256-5fdfadcb994ed1e0821c354e6b44320fd594dc97a9a68be775db087d69cc1f05?context=repo)

- then i signed out of docker using 
```sh
docker logout
```
<img width="883" alt="SCR-20230222-1gb" src="https://user-images.githubusercontent.com/112965272/220486573-07a6c315-9ad9-4048-a655-08870f46ca24.png">

## Using multi-stage building for a Dockerfile build

What is multi-stage build?

With multi-stage builds, you use multiple `FROM` statements in your Dockerfile. Each `FROM` instruction can use a different base, and each of them begins a new stage of the build. You can selectively copy artifacts from one stage to another, leaving behind everything you don't want in the final image.

Some Docker keyword in a multi stage build are :

- `AS` = This is used to name the first image in the first `FROM` statement in our dockerfile
- `COPY --from=build <source image> <destination image> `

in this challenge i recreated the Frontend cruddur react app to use a multi stage build in the docker file.

Please find the code below👇

```dockerfile

## This is the first image created in the process named <build> so it can be referenced.
FROM node:19.7-slim AS build

## Here we are copying contents from our local machine to the container to a new directort /frontend-react-js  inside the container.
COPY . /frontend-react-js

## Change the working directory 
WORKDIR /frontend-react-js

## RUn NPM install inside the working directory above
RUN npm install

## create a new image from a different version of node
FROM node:16.18

## Expose a port for the container to listen on
EXPOSE ${PORT}

## copies the content of /frontend-react-js from the first image (node:19.7-slim) to a folder name /frontend-react-js in the second image(node:16.18) as well as all the files and dependencies gotten from running npm install .
COPY --from=build /frontend-react-js /frontend-react-js

## change the working directory to the directory needed to run our app
WORKDIR /frontend-react-js

## Health check to ping this url and return a value of healthy or unhealthy
HEALTHCHECK CMD curl --fail http://localhost:3000/ || exit 1

## execute this command to start npm
CMD ["npm", "start"]

```

After the build process is complete, image1 `(node:19.7-slim)` is discarded and image2  `(node:16.18)` is finalized .

What Multi-Stage Builds Can Be Good For
- Reduce Redundant Build Effort. ...
- Make Intermediate Image Layers Shareable. ...
- Keep Final Images Small And Dockerfiles Readable. ...
- Keep Your Secrets Safe. ...
- Simplify Your Image Stages Internally. ...
- Notice You Might Have Deeper Lying Problems.

Using the image below , notice my multi build image of the same application is smaller in size compare to the non multi build image.

<img width="1127" alt="SCR-20230223-1dt" src="https://user-images.githubusercontent.com/112965272/220792367-39423686-d81b-4d32-81f5-b362d2f3c243.png">

## Run the dockerfile CMD as an external script

What is the `CMD` command in the dockerfile?

When building a Dockerfile, the CMD instruction specifies the default program that will execute once the container runs.

`CMD` Sets default parameters that can be overridden from the Docker Command Line Interface (CLI) when a container is running.

### Steps to Running CMD as an external script in a Dockerfile

- Create a shell script in the same folder as your dockerfile
- write the CMD command inside the shell script .sh file
- give permission to the shell script using `chmod x+`
- Call the shell script inside the dockerfile in the CMD line

i Used the dockerfile for the frontend-react as an example to demostrate this homework challenge.

shell script to initialise npm in the dockerfile in /frontend-react-js folder 

![SCR-20230223-x1o](https://user-images.githubusercontent.com/112965272/221048206-7b96cd77-8531-480d-b457-7f60d9d39eb6.png)


```sh
#!/bin/bash
npm start
```
now calling the  shell script inside the docker file will look like this 

```dockerfile

FROM node:16.18

ENV PORT=3000

COPY . /frontend-react-js

## copy npm-start.sh from outside container to inside the container 
COPY npm-start.sh npm-start.sh

## giave permission to the file so it can be executed
RUN chmod +x npm-start.sh

WORKDIR /frontend-react-js

RUN chown -R node:node /frontend-react-js
USER node

RUN npm install
EXPOSE ${PORT}

## Passing the script into the CMD to start npm
CMD ["sh", "npm-start.sh"]

```

Another method to achieve this is to pass this command into the terminal 

```Sh
docker run --name react -d -p 3000:3000 reactapp npm start
```

## Implement a healthcheck in the V3 Docker compose file

What is a docker health Check ?


The `HEALTHCHECK` directive tells Docker how to determine if the state of the container is normal. This was a new directive introduced during Docker 1.12. Before the HEALTHCHECK directive, the Docker engine can only determine if the container is in a state of abnormality by whether the main process in the container exits. In many cases, this is fine, but if the program enters a deadlock state, or an infinite loop state, the application process does not exit, but the container is no longer able to provide services.


The syntax look like:
```sh
HEALTHCHECK [options] CMD <command>:
```

The above syntax set the command to check the health of the container.


### How does it work?
When a `HEALTHCHECK `instruction is specified in an image, the container is started with it, the initial state will be starting, and will become healthy after the HEALTHCHECK instruction is checked successfully. If it fails for a certain number of times, it will become unhealthy.

i implemented the the health check on the docker compose file by passing the healtcheck column with it properties 

```docker-compose.yml
healthcheck:
      ## The parameter which the test will be based on
      test: ["executable", "arg"]
      
      ## the time in between each test
      interval: 1m30s
      
      ##time to rule the container healthy or unhealthy
      timeout: 30s
      
      #number of times the healthcheck will retry
      retries: 5
      
      ## The wait time for container to initialize before the check starts
      start_period: 30s
      
```
  With the above synthax i was able to implement a `healthcheck` on the docker-compose file for the `cruddur app`👇
  
  ```yaml
  
  version: "3.8"
services:
  backend-flask:
    environment:
      FRONTEND_URL: "http://localhost:3000"
      BACKEND_URL: "http://localhost:4567"
    build: ./backend-flask
    ports:
      - "4567:4567"
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:4567/api/activities/home"]
      interval: 1m30s
      timeout: 10s
      retries: 3
      start_period: 40s
    volumes:
      - ./backend-flask:/backend-flask
  frontend-react-js:
    environment:
      REACT_APP_BACKEND_URL: "http://localhost:4567"
    build: ./frontend-react-js
    depends_on:
      backend-flask: 
        condition: service_healthy
    ports:
      - "3000:3000"
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000"]
      interval: 1m30s
      timeout: 10s
      retries: 3
      start_period: 40s
    volumes:
      - ./frontend-react-js:/frontend-react-js
  dynamodb-local:
    # https://stackoverflow.com/questions/67533058/persist-local-dynamodb-data-in-volumes-lack-permission-unable-to-open-databa
    # We needed to add user:root to get this working.
    user: root
    command: "-jar DynamoDBLocal.jar -sharedDb -dbPath ./data"
    image: "amazon/dynamodb-local:latest"
    container_name: dynamodb-local
    ports:
      - "8000:8000"
    volumes:
      - "./docker/dynamodb:/home/dynamodblocal/data"
    working_dir: /home/dynamodblocal

  db:
    image: postgres:13-alpine
    restart: always
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    ports:
      - '5432:5432'
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 1m30s
      timeout: 30s
      retries: 5
      start_period: 30s
    volumes: 
      - db:/var/lib/postgresql/data

# the name flag is a hack to change the default prepend folder
# name when outputting the image names
networks: 
  internal-network:
    driver: bridge
    name: cruddur

volumes:
  db:
    driver: local
    
```
  
  
## HealthCheck for backend-flask

![SCR-20230224-ql](https://user-images.githubusercontent.com/112965272/221055576-771cab11-92d5-4880-a252-31cb9dcc8421.png)


## HealthCheck for frontend-react-js app

![SCR-20230224-z2](https://user-images.githubusercontent.com/112965272/221056531-8affdca8-15aa-4a5e-8215-526108da72eb.png)


## HealthCheck for Postgres Database

![SCR-20230224-12z](https://user-images.githubusercontent.com/112965272/221057604-9a071914-aa65-4dcc-872c-c2d5fb393199.png)


  
      







 
 
 











 

 


