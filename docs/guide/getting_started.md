# Getting Started: Installing the Backend Service

Welcome to the ProjectPath-Pro backend installation guide. This section will help you set up the backend service and database service using Docker to ensure a seamless installation and execution experience.

## Prerequisites 

Before you begin, ensure you have the following installed on your system (we'll asume you are using Windows):

- **Docker:** [Install Docker](https://www.docker.com/)
- **Git:** [Install Git](https://git-scm.com/downloads/win)
- **Maven:** [Install Maven](https://maven.apache.org/install.html)

## Steps to Install and Run the Backend

### 1. Clone the Repository

Start by cloning the repository to your local machine:

```bash
git clone https://github.com/gersonvidal/ProjectPath-Pro-Backend
cd ProjectPath-Pro-Backend
```

### 2. Build the Spring Boot project

Execute the following command inside `ProjectPath-Pro-Backend`

```bash
./mvnw clean install
```

This command will delete files generated in previous compilations to ensure a clean environment. Also, it will execute all the necessary phases to create a valid and working `.jar` file that will be used by Docker to generate the container.

### 3. Build and Start the Docker Containers

The repository includes a Dockerfile and a docker-compose.yml file to simplify the setup process. You can start the backend service with a single command:

```bash
docker-compose up --build
```

This command will:

- Build the Docker image for the backend.
- Start the containers with the database and the backend service respectively.

### 4. Verify the Service

Once the containers are up and running, verify that the backend is working by visiting the API's health check endpoint (or any available endpoint). By default, the service will be available at:

```bash
http://localhost:8080
``` 

You should see that the API sends an error message saying something like "unauthorized". This means it is working because you haven't registered a user to log in. 

<div style="color: green; font-weight: bold; font-size: 1.5em; text-align: center;">
    CONGRATULATIONS!
</div>

## Stop the services

To stop the running containers, use the following command:

```bash
docker-compose down
```

This will gracefully shut down the backend service and database service.