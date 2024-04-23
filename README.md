# Running Three Tier Application using Docker

To run Three Tier Application using Docker, we need to first create a Full Stack Project. We can use any of our Full Stack Project but if we don't have one then we can use someone else project also. Here, I have used a very easy simple project on GitLab. The link to this project is https://gitlab.com/nanuchi/developing-with-docker. This project is made using HTML, CSS, JavaScript, Express and MongoDB. 

- First you need to clone the Project Repository from the link using command<br> ```git clone https://gitlab.com/nanuchi/developing-with-docker```

To run a Three Tier Application, we need to run three Docker Containers simultaneously. So, it is necessary to run all these containers inside a network to avoid their interaction with other containers.

- So, Network can be created by using the following command after starting the Docker Engine with name __mongo-network__ <br> ```docker netowrk create mongo-network```
     ![image](https://github.com/Karans2100/Docker-Blog/assets/104305073/e44e27df-6c1e-454d-b743-f56637cc3088)

You can see all the networks using the command: ```docker network ls```. Now, we need to pull the image of MongoDB from DockerHub and make container from it. Then we need to run this container in the network to access MongoDB Database using Docker Container.
![image](https://github.com/Karans2100/Docker-Blog/assets/104305073/fa855089-a137-494d-b38f-0341bd2858f9)

- To pull MongoDB image from DockerHub and run it as container we need to execute the command <br> ```docker run -p 27017:27017 -e MONGO_INITDB_ROOT_USERNAME=admin -e MONGO_INITDB_ROOT_PASSWORD=password --network=mongo-network --name=21BCP377-mongodb -d mongo```
   ![image](https://github.com/Karans2100/Docker-Blog/assets/104305073/abe81a0e-ad31-46fc-bcc2-0d650e6fe663)

Here, the __mongo__ image will be automatically pull from the DockerHub to run the Container in __detachable mode__ with name __*21BCP377-mongodb*__ in the network __mongo-network__. The Container will be running on the default *27017 port*. You can check all the running containers using command ```docker ps```. Environment Variables *Username* and *Password* are also passed to run the container. Similarly, we will be creating another container of *Express* by pulling its image from the DockerHub.

- Express Container can be run by using the follow command <br>```docker run -p 8081:8081 -e ME_CONFIG_MONGODB_ADMINUSERNAME=admin -e ME_CONFIG_MONGODB_ADMINPASSWORD=password -e ME_CONFIG_MONGODB_SERVER=21BCP377-mongodb --network=mongo-network --name=21BCP377-express -d mongo-express```
   ![image](https://github.com/Karans2100/Docker-Blog/assets/104305073/d32ce0d5-9efd-4d35-bf78-6bbf80509ee6)

Here, the container with name __*21BCP377-express*__ is made to connect the MongoDB Database with the Frontend of the project in Docker. As explained above, the container will run on *8081 port*. The environment variables Username and Password are passed of the MongoDB to access the Database along with the container name of the MongoDB in the Server Environment variable. The container will be running on the same network as the above container.

- Now, open the link in your browser ```localhost:8081``` and create two databases __my-db__ and __user-accounts__.
   ![image](https://github.com/Karans2100/Docker-Blog/assets/104305073/46c49839-8d83-49c1-8640-3fe39ef0299f)

Creating these two databases is necessary as the Frontend will be requiring these while running. Also, we need to change the MongoDB URL mentioned in the ```server.js``` file as we will be running MongoDB using Docker instead of Local Machine.

- Naviagate to the ```server.js``` file in the project and replace ```mongoUrlLocal``` and ```mongoUrlDocker``` with the following value ```mongodb://admin:password@21BCP377-mongodb:27017```
   ![image](https://github.com/Karans2100/Docker-Blog/assets/104305073/37fc5783-5ac0-4b9d-a661-ca849a5794eb)

- Now, we need to delete the Dockerfile already provided in the project and create a new Dockerfile inside the ```app``` folder of the project with the following commands<br> <pre>```FROM node
WORKDIR /app
COPY . .
ENV MONGO_DB_USERNAME=admin
ENV MONGO_DB_PWD=password
RUN npm install
EXPOSE 3000
CMD ["node", "server.js"]```</pre>

![image](https://github.com/Karans2100/Docker-Blog/assets/104305073/7eedda83-3b86-423a-92a2-7138992b27ca)

All the explanation of the above commands of the Dockerfile can be understood here: https://github.com/Karans2100/Docker-File. Now, we need to create a Docker Image by building the Docker file to run it as a container in the network. 

- Open the __CMD__ Terminal inside the ```app``` folder of the project and run the command <br> ```docker build -t 21bcp377-project```
    ![image](https://github.com/Karans2100/Docker-Blog/assets/104305073/c47f6d1e-5304-4306-95e3-e24ce9bf56fd)

The execution of the above command will generate a Docker Image with name __21bcp377-project__ and it will take some time for it. After the Image is created we need to Run it as a Docker Container.

- To run the Image as a Docker Container run the command <br> ```docker run -d -p 3000:3000 21bcp377-project --network=mongo-network --name=21bcp377-project```
    ![image](https://github.com/Karans2100/Docker-Blog/assets/104305073/03c3c0cb-ad26-414f-bbf8-4e53cdf097c1)

After the above command is executed, it will run a Docker Container in *detachable mode* with name __21bcp377-project__ in the same network. 

- Now, open the following link on you browser ```localhost:3000``` and you will see a webpage running.
    ![image](https://github.com/Karans2100/Docker-Blog/assets/104305073/3f75ced2-d605-4c02-b0ae-5eb9fa22f593)
    ![image](https://github.com/Karans2100/Docker-Blog/assets/104305073/982ed4d2-0e28-4d27-8c03-bbaf9ae7e1f8)
    ![image](https://github.com/Karans2100/Docker-Blog/assets/104305073/17f341ac-acaa-42cf-a1e1-297e94002cd0)
    ## Note: I have changed the code in the project so you might see some difference in the Images.

- To push the Docker Image of the project run the command <br> ```docker tag 21bcp377-project karan2100/21bcp377-project-image``` and then ```docker push karan2100/21bcp377-project-image```
    ![image](https://github.com/Karans2100/Docker-Blog/assets/104305073/7f50afd8-cfcc-4d5c-8a63-fca1ea2b2075)

Here, change __karan2100__ with your Dockerhub username and __21bcp377-project-image__ with your Dockerhub Repository name.
