# n8n Setup with Ollama and PostgreSQL on Ubuntu Linux Server with Docker
This guide provides step-by-step instructions to set up n8n, Ollama, and PostgreSQL on an Ubuntu Linux server using Docker containers. n8n is an open-source workflow automation tool, Ollama is an AI-powered assistant, and PostgreSQL serves as the database for n8n.

Prerequisites
Before starting, ensure you have the following installed on your Ubuntu Linux server:

Docker: Docker allows you to create, deploy, and run applications in containers.
Docker Compose: A tool to define and manage multi-container Docker applications.
1. Install Docker
Run the following commands to install Docker on your Ubuntu server:

bash
Copy
sudo apt update
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt update
sudo apt install -y docker-ce
sudo systemctl enable docker
sudo systemctl start docker
2. Install Docker Compose
Next, install Docker Compose by running:

bash
Copy
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
Verify the installation:

bash
Copy
docker-compose --version
3. Create Project Directory
Create a directory where you will store the Docker Compose configuration and other necessary files.

bash
Copy
mkdir -p ~/n8n-ollama-postgres
cd ~/n8n-ollama-postgres
4. Docker Compose Setup
In the ~/n8n-ollama-postgres directory, create a docker-compose.yml file. This file will define the n8n, Ollama, and PostgreSQL services.

docker-compose.yml
yaml
Copy
version: "3.7"
services:
  # PostgreSQL Service
  postgres:
    image: postgres:13
    container_name: postgres
    environment:
      - POSTGRES_USER=n8n
      - POSTGRES_PASSWORD=n8npassword
      - POSTGRES_DB=n8n
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"
    networks:
      - n8n_network

  # n8n Service
  n8n:
    image: n8nio/n8n
    container_name: n8n
    environment:
      - DB_TYPE=postgresdb
      - DB_POSTGRESDB_HOST=postgres
      - DB_POSTGRESDB_PORT=5432
      - DB_POSTGRESDB_DATABASE=n8n
      - DB_POSTGRESDB_USER=n8n
      - DB_POSTGRESDB_PASSWORD=n8npassword
      - N8N_HOST=n8n.local
      - N8N_PORT=5678
      - N8N_PROTOCOL=http
      - VUE_APP_URL_BASE_API=http://n8n.local:5678
    ports:
      - "5678:5678"
    depends_on:
      - postgres
    networks:
      - n8n_network

  # Ollama Service
  ollama:
    image: ollama/ollama:latest
    container_name: ollama
    ports:
      - "8000:8000"
    networks:
      - n8n_network

networks:
  n8n_network:
    driver: bridge

volumes:
  postgres_data:
    driver: local
5. Configuration Details
PostgreSQL: This service will run the PostgreSQL database used by n8n.
Username: n8n
Password: n8npassword
Database: n8n
n8n: The workflow automation tool that will connect to PostgreSQL.
PostgreSQL credentials are set in the environment section.
n8n will be accessible via http://<server-ip>:5678.
Ollama: The Ollama service is a container running the Ollama AI assistant.
6. Running the Containers
Now, you are ready to start the Docker containers. Run the following command to spin up the containers:

bash
Copy
docker-compose up -d
The -d flag runs the containers in detached mode. The containers will run in the background.

7. Accessing n8n
After the containers are up and running, you can access the n8n interface by navigating to:

cpp
Copy
http://<server-ip>:5678
Replace <server-ip> with the IP address of your server.

8. Verify Container Logs
You can check the logs to ensure everything is running correctly:

bash
Copy
docker-compose logs -f
9. Stopping the Containers
To stop the running containers, use the following command:

bash
Copy
docker-compose down
This will stop and remove the containers, but your data will persist due to the volumes defined in the docker-compose.yml file.

10. Troubleshooting
If you encounter issues with connectivity between containers, verify that the n8n_network is properly configured and that there are no network conflicts.
Check the logs using docker-compose logs for error messages.
Conclusion
You have successfully set up n8n with Ollama and PostgreSQL in Docker containers on your Ubuntu Linux server. You can now begin creating and automating workflows in n8n and leverage Ollama's capabilities.




