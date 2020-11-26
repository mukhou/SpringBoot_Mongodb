# spring-boot-mongodb

To run this application:

Run the main class: SpringBootMongodbApplication

**Steps to dockerize the application:**

**Method1:**
Create docker image and run container from command prompt:
1. cd into the root folder which has Docker file and run: docker build -t mukhou/springbootmongodb .
2. Start mongo container: docker run -p 27017:27017 mongo
3. In a separate command prompt, find out IP address of the mongo container created from above step using docker inspect <containerid>. It will be under Networks section 
4. Run docker image of the spring app built in #1: docker run -p 8080:8080 -e SPRING_DATA_MONGODB_.HOST=<IPaddress>  mukhou/springbootmongodb
3. Verify: http://localhost:8080/product/list

**Method2:**
From command prompt, cd into the root folder which has Docker file and directly run this command: docker-compose up

**Notes**: If you don't want to set the environment variable **SPRING_DATA_MONGODB_.HOST** in docker-compose file, 
 then do the following:
  set the below two properties in application.properties file:
   spring.data.mongodb.host=mongo
   spring.data.mongodb.port=27017

In this way, the spring app will connect to the **host of the service named "mongo" defined in the docker-compose file**, instead
of connecting to localhost, which it does by default.

**Steps to run this application on kubernetes:**

**Method1:**
Create a **single** deployment and service files for spring app and mongo:
1. build the docker image following above section(step #1, Method #1)
2. publish image to docker hub: docker push mukhou/springbootmongodb
3. cd into the root folder which has deployment and service file and run:
4. kubectl create -f deployment-all.yml
5. kubectl create -f service-definition.yml
6. verify pods, replicas, services and deployment created: kubectl get all
7. forward port to access application from cluster: kubectl port-forward svc/springbootmongodb 8080:8080
8. Verify: http://localhost:8080/product/list


**Method2:**
Create a **separate** deployment files and service files for spring app and mongo:
1. build the docker image following above section(step #1, Method #1)
2. publish image to docker hub: docker push mukhou/springbootmongodb
3. cd into the root folder which has deployment and service file and run:
4. Create mongo deployment: kubectl create -f deployment-mongo.yml
5. Create mongo service: kubectl create -f service-mongo.yml
6. verify pods, replicas, services and deployment created: kubectl get all
7. Create app deployment: kubectl create -f deployment-definition.yml
8. Create app service: kubectl create -f service-definition.yml
9. verify pods, replicas, services and deployment created: kubectl get all
10. forward port to access application from cluster: kubectl port-forward svc/springbootmongodb 8080:8080
11. Verify: http://localhost:8080/product/list


**Notes**
In deployment-definition.yml file, we need to set the env variable for spring data mongo db host to that of the 
mongo db service created, similar to how we did in docker-compose.yml file for dockerizing the app(if using that approach).

