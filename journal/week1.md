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

# we are copying the contents of the frontend-react-js 
COPY . /frontend-react-js
WORKDIR /frontend-react-js
RUN npm install
EXPOSE ${PORT}
CMD ["npm", "start"]
 
 
 
 
 











 

 


