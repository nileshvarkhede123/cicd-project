Exercise 2: CI/CD Pipeline for a Java Application
Sample app : https://github.com/in28minutes/spring-boot-examples/tree/master/spring-boot-2-rest-service-with-swagger
You need to automate the deployment of a Spring Boot application hosted in a GitHub repository. The pipeline will:

    Pull code from GitHub
    Build the application using Maven
    Run unit tests
    Containerize the app with Docker
    Push the image to Docker Hub
    Deploy the container on ECS
