# n8n with PostgreSQL and Ollama in Docker Compose (Ubuntu)

This README provides instructions for setting up n8n with PostgreSQL as its database and Ollama for local LLM inference, all running in Docker containers on an Ubuntu Linux environment.

## Prerequisites

* **Ubuntu Linux:** This guide assumes you are using an Ubuntu-based system.
* **Docker:** Install Docker Engine on your Ubuntu system. Follow the official Docker installation instructions: [https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/)
* **Docker Compose:** Install Docker Compose. It's often included with Docker Desktop, but if not, you can install it separately: [https://docs.docker.com/compose/install/](https://docs.docker.com/compose/install/)
* **Git (Optional):** Recommended for cloning the repository.

## Installation Steps

1.  **Clone the Repository (Optional):**

    If you have a repository containing your `docker-compose.yml` and `.env` files, clone it:

    ```bash
    git clone <your_repository_url>
    cd <your_repository_directory>
    ```

    Otherwise, create a directory and create the docker-compose.yml and .env files manually.

2.  **Create `docker-compose.yml`:**

    Create a `docker-compose.yml` file in your project directory with the following content:

    ```yaml
    version: "3.8"
    services:
      postgres:
        image: postgres:15
        restart: always
        environment:
          POSTGRES_USER: ${POSTGRES_USER}
          POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
          POSTGRES_DB: ${POSTGRES_DB}
        volumes:
          - postgres-data:/var/lib/postgresql/data

      n8n:
        image: n8nio/n8n
        restart: always
        ports:
          - "5678:5678"
        environment:
          DB_TYPE: postgresdb
          DB_POSTGRESDB_HOST: postgres
          DB_POSTGRESDB_PORT: 5432
          DB_POSTGRESDB_DATABASE: ${POSTGRES_DB}
          DB_POSTGRESDB_USER: ${POSTGRES_USER}
          DB_POSTGRESDB_PASSWORD: ${POSTGRES_PASSWORD}
          N8N_HOST: ${N8N_HOST} # e.g., localhost, or your domain
          N8N_PORT: 5678
          N8N_BASIC_AUTH_ACTIVE: "true"
          N8N_BASIC_AUTH_USER: ${N8N_USER}
          N8N_BASIC_AUTH_PASSWORD: ${N8N_PASSWORD}
          N8N_EXECUTIONS_MODE: queue
          EXECUTIONS_DATA_SAVE_ON_DISK: "true"
          GENERIC_TIMEZONE: "your/timezone" # e.g., Europe/Berlin or America/New_York
          N8N_ENCRYPTION_KEY: ${N8N_ENCRYPTION_KEY}
          OLLAMA_BASE_URL: http://ollama:11434
        depends_on:
          - postgres
          - ollama
        volumes:
          - n8n-data:/root/.n8n

      ollama:
        image: ollama/ollama
        restart: always
        ports:
          - "11434:11434"
        volumes:
          - ollama-data:/root/.ollama

    volumes:
      postgres-data:
      n8n-data:
      ollama-data:
    ```

3.  **Create `.env` File:**

    Create a `.env` file in the same directory as `docker-compose.yml` and add the following variables. Replace the placeholder values with your own:

    ```dotenv
    POSTGRES_USER=n8n
    POSTGRES_PASSWORD=your_postgres_password
    POSTGRES_DB=n8n
    N8N_HOST=localhost # or your domain
    N8N_USER=n8nuser
    N8N_PASSWORD=your_n8n_password
    N8N_ENCRYPTION_KEY=your_n8n_encryption_key # Generate a strong random key.
    ```

    **Important:**

    * Generate a strong and unique `N8N_ENCRYPTION_KEY`. You can use a command like `openssl rand -base64 32` to generate one.
    * Change the timezone in the docker-compose.yml file.

4.  **Start the Containers:**

    Run the following command in the directory containing your `docker-compose.yml` and `.env` files:

    ```bash
    docker-compose up -d
    ```

    This will download the necessary images and start the containers in detached mode.

5.  **Access n8n:**

    Open your web browser and go to `http://localhost:5678` (or your configured `N8N_HOST` and port). You will be prompted to enter the username and password you set in the `.env` file.

6.  **Pull an Ollama Model:**

    To use Ollama in n8n, you must first pull the model you'd like to use. You can do this by entering the ollama container:

    ```bash
    docker exec -it <ollama_container_name> /bin/bash
    ```

    Find the ollama container name using `docker ps`. Then pull the desired model:

    ```bash
    ollama pull llama2
    ```

    Replace `llama2` with your model of choice.

7. **Use Ollama in n8n**

    In n8n, install the ollama node. You can then use the ollama node, and set the model you previously pulled.

## Stopping the Containers

To stop the containers, run:

```bash
docker-compose down
