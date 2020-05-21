# TFG Port Scanner
This is the full project on docker-compose. In order to run it, you will need **Docker** on your computer.

To get docker you can get it [here](https://docs.docker.com/get-docker/, "Docker download site").

***

## Structure

In order for this project to work you must create the following files and folders:
* **mongo** (Folder). It's important to create this folder because it will store all the data from MongoDB to your computer.
* **static** (Folder). This folder will be used to create the images that users will upload there to.
* **default_avatar.jpg** (File-image). This image must go to the **static** folder at the root level. This image is the default profile image that users will get if they didn't login with a google account. You can find any on google, just **make sure it's a jpg** and the name is **default_avatar**, without any capitals.
* **.env** (File-environment). This file must be at the root of this folder. Check .env.sample file here.
* **env** (Folder). This folder will contain a **.env** file which will be used by the front-end client. Check it's own **.env.sample**.

***
## Running it
To run this project, first you have to type in the terminal to ensure you are up to date with the versions on the hub.
```
docker-compose pull
``` 

Finally, run this command for full application logging.
```
docker-compose up
```

If you don't want any logging and you just want it to run in the background run it in detached mode by using the following command.
```
docker-compose up -d
```

***
## Considerations

### JWT Secret
The backend server relies on json web tokens (JWT for short) and you shall need a secret. You should use any random string, since you will not need to remember it. 

We recommend (since it's a node.js based project) to run this command on a **node.js** command line (you can get it by opening a terminal and typing in **node**)
```javascript
require('crypto').randomBytes(48, function(err, buffer) { 
    console.log(buffer.toString("hex")); 
})
```
### Backend port
If you change the port in the **.env** file that corresponds to the backend, make sure you also change the port it runs internally in the docker-compose file.
```yml
services:
    backend:
        container_name: portscanner-backend
        restart: on-failure
        image: tfgportscanner/portscanner-backend:latest
        volumes:
            - $PWD/static:/app/static
        ports:
            - "8000:<change-me>"
        environment:
            - PORT=${PORT}
            - WHITELIST=${WHITELIST}
            - SECRET=${SECRET}
            - SALT_ROUNDS=${SALT_ROUNDS}
            - GOOGLE_CLIENT_ID=${GOOGLE_CLIENT_ID}
            - GOOGLE_CLIENT_SECRET=${GOOGLE_CLIENT_SECRET}
            - MONGO_USER=${MONGO_USER}
            - MONGO_PASSWORD=${MONGO_PASSWORD}
            - MODE=production
            - NODE_ENV=production
```

### MongoDB on a cloud server
If you dont want to create a separate container for MongoDB, you need to remove the variables MONGO_USER and MONGO_PASSWORD from the .env file of the backend and create a new one with the complete url to that server (including your username and password). Then finally remove this portion of the **docker-compose** file:

```yml
mongo:
        container_name: mongo
        image: mongo
        volumes:
            - $PWD/mongo:/data/db
        ports:
            - "27017:27017"
        environment:
            - MONGO_INITDB_ROOT_USERNAME=${MONGO_USER}
            - MONGO_INITDB_ROOT_PASSWORD=${MONGO_PASSWORD}
            - MONGO_INITDB_DATABASE=portscanner
```

***

### Troubleshooting

***It's up and running but i get the default nginx gateway***

Do not worry, this application uses quasar CLI (Command Line Interface) and it runs internally the command **quasar build** and it takes a while for it to compile. Just wait a little and it will be done.

***I added my domains on the whitelist for CORS and it doesnt work***

First, make sure that the domains are split by commas (,) and do not contain a bar at the end of the url, such as http://localhost(/).

Also make sure they do not have whitespaces between them. 

This is an invalid example:
http://localhost:8080, http://localhost:8081

This is a valid example:
http://localhost:8080,http://localhost:8081

Also, you should do the same for the back-end server url in the environment file for the front-end.

***I changed something in the backend or in the front-end but i do not see the changes***

Keep in mind that this is the production version. If you want to make any changes you must go to the container and change it yourself in the src (or src-electron) folder.