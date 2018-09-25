# Dockerized Angular6 Application

This is a example project to show how to dockerize an Angular6 App and run it on a docker host. PLease make sure to have Docker installed on your host machine!!

### How to:

#### Setup Angular
- First of all please make sure to have the latest Angular Version installed. You can check that by typing:
```sh
$ ng version
```
- If Angular is not installed, please install it with:
```sh
$ npm install -g @angular/cli
```
- Create a new Angular project by using the Angular cli with the following command: 
```sh
$ ng new dockerizedAngular
```
- When the Angular cli setup your project successfully, you can try to run it via:
```sh
$ ng serve
```
After that you should be able to check out your app on: [http://localhost:4200]


#### Dockerize Application
- The best and prefered way to serve an angular application is to do this by using nginx. nginx is a lightweight webserver which is publicly available as an image on Dockerhub. nginx needs a configuration file which will be used later. Therefore please create a new file called nginx.conf in the root of your project and paste in the following contents. You don't have to understand the contents in all depth by now.
```sh
worker_processes  1;

events {
    worker_connections  1024;
}

http {
    server {
        listen 80;
        server_name  localhost;

        root   /usr/share/nginx/html;
        index  index.html index.htm;
        include /etc/nginx/mime.types;

        gzip on;
        gzip_min_length 1000;
        gzip_proxied expired no-cache no-store private auth;
        gzip_types text/plain text/css application/json application/javascript application/x-javascript text/xml application/xml application/xml+rss text/javascript;

        location / {
            try_files $uri $uri/ /index.html;
        }
    }
}
```
- The next step is to build our application by using the Angular cli:
```sh
$ ng build --prod
```

The ```--prod``` flag teels the cli to makle a production build with optimized scripts and assets.
- To make your application runnable on Docker within (the webserver)
 nginx we create a new file called ```Dockerfile```. In this file we can tell Docker about the steps to take:

```sh
// Get the nginx image in the alpine version
FROM nginx:alpine

// Copy th nginx.con file to the configuration folder within the image
COPY nginx.conf /etc/nginx/nginx.conf

// Create a working directory where our main html content (which should be served) will be stored.
WORKDIR /usr/share/nginx/html

// Copy all of our assets into this folder
COPY dist/dockerizedAngular .
```
- To finish the dockerization of our Angular application we create a new script in our ```package.json```:
```sh
"buildDocker": "ng build --prod && docker image build -t dockerized-angular-app ."
```
This command will do the automating in the creation of our Docker image. The command should finish and your image should be created successfully.

- You can now run your application by typing in your hosts cli:
```sh
$ docker run -p 3000:80 dockerized-angular-app
``` 

This command starts the image within a docker container and lets you browse it: ```http://localhost:3000```


[//]: # (These are reference links used in the body of this note and get stripped out when the markdown processor does its job. There is no need to format nicely because it shouldn't be seen. Thanks SO - http://stackoverflow.com/questions/4823468/store-comments-in-markdown-syntax)


   [http://localhost:4200]: <http://localhost:4200>
