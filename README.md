# Java Banking System using CQRS Event Sourcing with Kafka

This project is a Java-based implementation of a Banking System using CQRS (Command Query Responsibility Segregation) and Event Sourcing patterns, with Kafka as the event streaming platform.

## Table of Contents

- [Introduction](#introduction)
- [Features](#features)
- [Requirements](#requirements)
- [Usage](#usage)
- [API_Endpoints](#api_endpoints)
- [Notes](#notes)

## Introduction

In this project, I aim to build a scalable and resilient microservices architecture using Java, namely, a banking system. We leverage the CQRS pattern to separate the read and write concerns of our system, and the Event Sourcing pattern to store and replay events for maintaining the system's state.

## Features

### Microservices architecture with independent deployable units

The Bank Account query API is responsible for read information about the acounts from the database, while the Account Command API is responsible for manipulating accounts.

### Command Query Responsibility Segregation (CQRS) pattern

The CQRS pattern is commonly used in scenarios where there is a need for high scalability and performance, as it allows for independent scaling of the read and write operations. It is useful in systems with complex domain models and heavy read and write operations. By separating the read and write concerns, the CQRS pattern enables developers to optimize each model for its specific purpose. The write model can be designed for high throughput and consistency, while the read model can be optimized for fast and efficient querying.

In this context the CQRS pattern is used to separate the Bank Account query API from the Account Command API . This separation allows for independent scaling and optimization of the read and write operations, resulting in a more scalable and resilient microservices architecture.

### Event Sourcing for maintaining system state
Kafka is used to store and manage the events generated by the banking system. Each event represents a change in the state of the system, such as creating a new account, depositing or withdrawing funds, or transferring money between accounts.

When an event occurs, it is published to a Kafka topic, which acts as a durable and fault-tolerant log of events. The events are then consumed by the relevant microservices within the system, allowing them to update their own state accordingly.

## Requirements
You need to have the following installed to be able to run:
* Java Development Kit (JDK)
* Apache Maven
* Postman
* Docker
* Docker compose
* Apache Kafka
* MongoDB (any client)
* MySQL

Create a Docker network `bank`, or any other name to communicate between Docker containers:
```
docker network create --attachable -d bridge <docker_name>
```

Set up Apache Kafka using Docker. Create a `docker-compose.yml` file with the specified contents (present in the repo) and execute the following command to start Kafka:
```
docker-compose up -d
```

Run MongoDB within a Docker container linked to the docker network you created:
```
docker run -it -d --name mongo-container -p 27017:27017 --network <docker_name> --restart always -v mongodb_data_container:/data/db mongo:latest
```

Deploy MySQL in Docker and manage it using Adminer:
```
docker run -it -d --name mysql-container -p 3306:3306 --network <docker_name> -e MYSQL_ROOT_PASSWORD=password --restart always -v mysql_data_container:/var/lib/mysql mysql:latest

docker run -it -d --name adminer -p 8080:8080 --network <docker_name> -e ADMINER_DEFAULT_SERVER=mysql-container --restart always adminer:latest
```

Access MySQL through Adminer on port 8080, or your specified port.

## Usage
The java files and the configuration files are set according the the file structure. If you decide to change it, then you also need to change the config files. (.pom files), or the source will not build.

1. **Maven Build**: 
   To compile the application and package it into a deployable format, navigate to the root directory of each Maven module and run the following command:
   ```
   mvn clean package
   ```
   This command cleans any compiled files in the target directory, recompiles the source code, and packages the binary output into a JAR file.

### Building and Running with Docker

1. **Docker Compose Build**:
   To build Docker images for each Maven module and run the containers, ensure that each module contains a `Dockerfile` and use the following command:
   ```
   docker-compose up --build
   ```
   This command builds (or rebuilds) the images as necessary and starts the containers specified in the `docker-compose.yml` file.

### Managing Docker Containers

1. **Stopping Services**:
   To stop running services without removing the containers, you can use:
   ```
   docker-compose stop
   ```

### Note on Docker Compose Configuration

- Ensure your `docker-compose.yml` file is configured to build the Docker images based on the Dockerfiles present in each Maven module. The typical structure includes defining services that specify the context and Dockerfile locations.
- The `docker-compose.yml` file should define all necessary services, networks, and volumes to ensure the components can communicate and persist data as required.

## API_Endpoints

Below are the defined endpoints for account management operations and querying account data.

### Account Command Service

The Account Command service handles direct operations on accounts such as opening, depositing, withdrawing, and closing accounts.

- **Open an Account**
  - **Endpoint:** `/api/v1/accounts`
  - **Method:** POST
  - **Description:** Create a new account.

- **Deposit Funds**
  - **Endpoint:** `/api/v1/accounts/{id}/deposit`
  - **Method:** PUT
  - **Description:** Deposit funds into an existing account specified by its unique ID.

- **Withdraw Funds**
  - **Endpoint:** `/api/v1/accounts/{id}/withdraw`
  - **Method:** PUT
  - **Description:** Withdraw funds from an existing account specified by its unique ID.

- **Close an Account**
  - **Endpoint:** `/api/v1/accounts/{id}`
  - **Method:** DELETE
  - **Description:** Close an existing account specified by its unique ID.

- **Restore Read Database**
  - **Endpoint:** `/api/v1/operations/database/restore`
  - **Method:** POST
  - **Description:** Restore the read database to sync with the current state of the accounts.

### Account Query Service

The Account Query service provides endpoints for retrieving information about accounts.

- **List of All Accounts**
  - **Endpoint:** `/api/v1/accounts`
  - **Method:** GET
  - **Description:** Retrieve a list of all accounts.

- **List Account Details**
  - **Endpoint:** `/api/v1/accounts/{id}`
  - **Method:** GET
  - **Description:** Retrieve details of a specific account by its unique ID.

## Notes

* All the files are generated using SpringBoot.
* Both the directories bank-account and cqrs-es need to be present in the same directory to compile.
* Ideally, you can use intelliJ as it will be able to configure the project on it's own as all files are provided.
* This project has been created with the help of the course Java Microservices: CQRS & Event Sourcing with Kafka
 by Sean Campbell at Udemy.