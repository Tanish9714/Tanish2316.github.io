# Running Three Tier Application using Docker

To run Three Tier Application using Docker, we need to first create a Full Stack Project. We can use any of our Full Stack Project but if we don't have one then we can use someone else project also. Here, I have used a very easy simple project on GitLab. The link to this project is https://gitlab.com/nanuchi/developing-with-docker. This project is made using HTML, CSS, JavaScript, Express and MongoDB. 

- First you need to clone the Project Repository from the link using command<br> ```git clone https://gitlab.com/nanuchi/developing-with-docker```

To run a Three Tier Application, we need to run three Docker Containers simultaneously. So, it is necessary to run all these containers inside a network to avoid their interaction with other containers.

- So, Network can be created by using the following command after starting the Docker Engine with name __mongo-network__ <br> ```docker netowrk create mongo-network```
     ![Screenshot 2024-04-24 001200](https://github.com/Tanish9714/tanish2316.github.io/assets/146628920/35aae8bf-d48f-4270-a3fc-93f9aea25fc4)


You can see all the networks using the command: ```docker network ls```. Now, we need to pull the image of MongoDB from DockerHub and make container from it. Then we need to run this container in the network to access MongoDB Database using Docker Container.
![Screenshot 2024-04-23 235818](https://github.com/Tanish9714/tanish2316.github.io/assets/146628920/1272d14f-76b7-4dcb-802f-8fff50485ad4)



- To pull MongoDB image from DockerHub and run it as container we need to execute the command <br> ```docker run -p 27017:27017 -e MONGO_INITDB_ROOT_USERNAME=admin -e MONGO_INITDB_ROOT_PASSWORD=password --network=mongo-network --name=21BCP377-mongodb -d mongo```
   ![Screenshot 2024-04-24 005439](https://github.com/Tanish9714/tanish2316.github.io/assets/146628920/a479f65b-7b8b-4219-9d87-770a3881a777)


Here, the __mongo__ image will be automatically pull from the DockerHub to run the Container in __detachable mode__ with name __*21BCP377-mongodb*__ in the network __mongo-network__. The Container will be running on the default *27017 port*. You can check all the running containers using command ```docker ps```. Environment Variables *Username* and *Password* are also passed to run the container. Similarly, we will be creating another container of *Express* by pulling its image from the DockerHub.

- Express Container can be run by using the follow command <br>```docker run -p 8081:8081 -e ME_CONFIG_MONGODB_ADMINUSERNAME=admin -e ME_CONFIG_MONGODB_ADMINPASSWORD=password -e ME_CONFIG_MONGODB_SERVER=21BCP377-mongodb --network=mongo-network --name=21BCP377-express -d mongo-express```
   ![Screenshot 2024-04-24 005828](https://github.com/Tanish9714/tanish2316.github.io/assets/146628920/33408de3-e6a3-4b0c-abba-7e289a4103c1)


Here, the container with name __*mongo-express*__ is made to connect the MongoDB Database with the Frontend of the project in Docker. As explained above, the container will run on *8081 port*. The environment variables Username and Password are passed of the MongoDB to access the Database along with the container name of the MongoDB in the Server Environment variable. The container will be running on the same network as the above container.

- Now, open the link in your browser ```localhost:8081``` and create two databases __my-db__ and __user-accounts__.
   ![Screenshot 2024-04-24 010132](https://github.com/Tanish9714/tanish2316.github.io/assets/146628920/6623073c-cf7e-4d1e-bfb1-c3294f3fca78)


Creating these two databases is necessary as the Frontend will be requiring these while running. Also, we need to change the MongoDB URL mentioned in the ```server.js``` file as we will be running MongoDB using Docker instead of Local Machine.

- Naviagate to the ```server.js``` file in the project and replace ```mongoUrlLocal``` and ```mongoUrlDocker``` with the following value ```mongodb://admin:password@mongodb:27017```
   ![image](https://github.com/Tanish9714/tanish2316.github.io/assets/146628920/9811b935-addf-4a80-9cf9-35a47b68ab6b)


- Now, we need to delete the Dockerfile already provided in the project and create a new Dockerfile inside the ```app``` folder of the project with the following commands<br> ```FROM node
WORKDIR /app
COPY . .
ENV MONGO_DB_USERNAME=admin
ENV MONGO_DB_PWD=password
RUN npm install
EXPOSE 3000
CMD ["node", "server.js"]```

![image](https://github.com/Tanish9714/tanish2316.github.io/assets/146628920/ebd30888-31dc-49f1-9c27-ef3c4c47347b)


All the explanation of the above commands of the Dockerfile can be understood here: https://github.com/Tanish9714/DockerFile. Now, we need to create a Docker Image by building the Docker file to run it as a container in the network. 

- Open the __CMD__ Terminal inside the ```app``` folder of the project and run the command <br> ```docker build -t mynodeapp```
   ![Screenshot 2024-04-24 013457](https://github.com/Tanish9714/tanish2316.github.io/assets/146628920/9629bee5-eb4d-4ee3-bead-21b924e9e7d6)


The execution of the above command will generate a Docker Image with name __mynodeapp__ and it will take some time for it. After the Image is created we need to Run it as a Docker Container.

- To run the Image as a Docker Container run the command <br> ```docker run -d -p 3000:3000 mynodeapp --network=21BCP366-network --name=nodejs_app```
    ![Screenshot 2024-04-24 021901](https://github.com/Tanish9714/tanish2316.github.io/assets/146628920/8d51208f-54ed-443c-a8c9-15ad5a668f3a)


After the above command is executed, it will run a Docker Container in *detachable mode* with name __nodejs_app__ in the same network. 

- Now, open the following link on you browser ```localhost:3000``` and you will see a webpage running.
    ![Screenshot 2024-04-24 021808](https://github.com/Tanish9714/tanish2316.github.io/assets/146628920/351250e3-8c8e-46dd-93a9-b7cae8561312)
    ![Screenshot 2024-04-24 021840](https://github.com/Tanish9714/tanish2316.github.io/assets/146628920/c49d2641-a0c5-400d-8176-cff592c2d9a2)
    ![Screenshot 2024-04-24 024213](https://github.com/Tanish9714/tanish2316.github.io/assets/146628920/5a7106c5-4061-4c9a-9586-18487a235092)

  
## Note: I have changed the code in the project so you might see some difference in the Output.

- To push the Docker Image of the project run the command <br> ```docker tag mynodeapp tanish2316/cloud-ia2-21bcp366:v1.0``` and then ```docker push tanish2316/cloud-ia2-21bcp366:v1.0```
   ![Screenshot 2024-04-24 023456](https://github.com/Tanish9714/tanish2316.github.io/assets/146628920/d86dc327-7e1e-43fe-b8b5-8f0a0bc21370)


Here, change __tanish2316__ with your Dockerhub username and __cloud-ia2-21bcp366__ with your Dockerhub Repository name.
