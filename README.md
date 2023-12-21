# B-DOP-200: Popeye - Introduction to DevOps

## Set Sail for the Amazing World of Containers

### Table of Contents
1. [General Description](#general-description)
2. [Docker Images](#docker-images)
   - [Poll](#poll)
   - [Result](#result)
   - [Worker](#worker)
3. [Docker Compose](#docker-compose)
4. [Technical Formalities](#technical-formalities)

---

## 1. General Description

Welcome aboard the Popeye DevOps journey! In this project, you will embark on an adventure to master the basics of containerizing applications and defining multi-container infrastructures using Docker and Docker Compose.

### Application Overview

You will be working on containerizing and defining the deployment of a simple web poll application. The application comprises five elements:

1. **Poll**: A Flask Python web application that gathers votes and pushes them into a Redis queue.
2. **Redis Queue**: Holds votes sent by the Poll application, awaiting consumption by the Worker.
3. **Worker**: A Java application that consumes votes from the Redis queue and stores them into a PostgreSQL database.
4. **PostgreSQL Database**: Persistsently stores the votes stored by the Worker.
5. **Result**: A Node.js web application that fetches votes from the database and displays the results.

The code for these applications is generously provided on the intranet's project page, so no need to worry about coding them!

### DevOps Emphasis

In the realm of DevOps, understanding the technologies you work with is crucial. Take the time to research and comprehend how to configure the technologies as needed.

## 2. Docker Images

You are required to create three images, each with specific specifications.

### Poll

- **Base Image**: Python official image
- **Dependencies Installation Command**: `pip3 install -r requirements.txt`
- **Run Command**: `flask run --host=0.0.0.0 --port=80`

### Result

- **Base Image**: Official Node.js version 12 Alpine image
- **Dependencies Installation Command**: `npm install` (be cautious about the location)
- **Note**: Exclude the `node_modules` folder from the build context.

### Worker

- **Build**: Multi-stage build
  - **First Stage - Compilation**:
    - Base Image: `maven:3.8.4-jdk-11-slim`
    - Commands: `mvn dependency:resolve` and `mvn package`
  - **Second Stage - Run**:
    - Base Image: `openjdk:11-jre-slim`
    - Run Command: `java -jar worker-jar-with-dependencies.jar`

- **General**: Keep images simple, lightweight, and avoid unnecessary components.
- **Note**: Hard-coded connection information should be replaced with environment variables.

## 3. Docker Compose

Now that you have three isolated images, it's time to orchestrate them with Docker Compose!

Create a `docker-compose.yml` file with the following specifications:

### Services

- **poll**:
  - Builds the Poll image
  - Redirects host port 5000 to container port 80

- **redis**:
  - Uses an official Redis image
  - Opens port 6379

- **worker**:
  - Builds the Worker image

- **db**:
  - Represents the PostgreSQL database
  - Uses an official PostgreSQL image
  - Creates the database schema during the container's first start

- **result**:
  - Builds the Result image
  - Redirects host port 5001 to container port 80

### Networks

- poll-tier: Allows communication between Poll and Redis
- result-tier: Allows communication between Result and Database
- back-tier: Allows communication between Worker, Redis, and Database

### Volumes

- db-data: Named volume for persistent database data

### Configuration

- Ensure databases launch before the services that depend on them.
- Containers should restart automatically when stopped unexpectedly.
- Define environment variables in the `docker-compose.yml` file.

## 4. Technical Formalities

Your project will be evaluated using automated tests by analyzing the configuration files. Your repository must contain the following files:

.<br/>
|-- docker-compose.yml<br/>
|-- schema.sql<br/>
|-- poll<br/>
|   |-- Dockerfile<br/>
|-- result<br/>
|   |-- Dockerfile<br/>
|-- worker<br/>
	|-- Dockerfile<br/>

The directories `poll`, `result`, and `worker` contain the provided applications from the intranet.