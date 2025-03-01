# n8n Setup with Ollama and PostgreSQL on Ubuntu Linux Server with Docker

This guide provides step-by-step instructions to set up **n8n**, **Ollama**, and **PostgreSQL** on an **Ubuntu Linux server** using **Docker** containers. **n8n** is an open-source workflow automation tool, **Ollama** is an AI-powered assistant, and **PostgreSQL** serves as the database for **n8n**.

## Prerequisites

Before starting, ensure you have the following installed on your Ubuntu Linux server:

- **Docker**: Docker allows you to create, deploy, and run applications in containers.
- **Docker Compose**: A tool to define and manage multi-container Docker applications.

### Step 1: Install Docker

Run the following commands to install Docker on your Ubuntu server:

```bash
sudo apt update
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt update
sudo apt install -y docker-ce
sudo systemctl enable docker
sudo systemctl start docker


