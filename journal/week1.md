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


- After running the above command we make shure we unlock the port inn the port tab.

 

 


