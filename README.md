# Stage 6: Scalable Infrastructure for Document-Aware Chatbot

## Overview
Stage 6 of the SDA-Chatbot project introduces a production-ready architecture designed for scalability and robustness. This phase integrates advanced infrastructure components to support a Retrieval-Augmented Generation (RAG) chatbot, enabling accurate, context-aware responses based on uploaded PDF documents.

## Architecture Components

### 1. User Interaction
Users interact with the chatbot via a Streamlit-based web interface. They can chat casually or upload PDF files to receive document-specific responses.

### 2. Secure Azure Virtual Machine (VM)
The core services are hosted on a secure Azure VM, which includes:
- **Network Security Group (NSG)**: Manages inbound/outbound traffic.
- **Disk Storage**: Ensures data persistence.
- **Network Interfaces**: Handles external and internal network communications.

### 3. Backend Services
- **Streamlit** serves the frontend.
- **FastAPI** handles the backend logic and LLM integration.

### 4. PostgreSQL Database
Stores chat history, metadata, and user interactions securely.

### 5. Chroma Vector Store
Enhances the chatbot's ability to retrieve relevant content from uploaded PDFs, improving response accuracy.

### 6. GitHub Actions (CI/CD)
Automates the deployment and update process using GitHub Actions for continuous integration and delivery.

---

## Getting Started

### Step 1: Initialize the VM with Custom Data
Use this script when creating the VM:
```bash
#!/bin/bash
sudo apt update
sudo apt install -y gnupg2 wget

# Install Miniconda
sudo -u azureuser mkdir -p /home/azureuser/miniconda3
sudo -u azureuser wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O /home/azureuser/miniconda3/miniconda.sh
sudo -u azureuser bash /home/azureuser/miniconda3/miniconda.sh -b -u -p /home/azureuser/miniconda3
sudo -u azureuser rm /home/azureuser/miniconda3/miniconda.sh

# Set PATH
echo 'export PATH="/home/azureuser/miniconda3/bin:$PATH"' | sudo -u azureuser tee -a /home/azureuser/.bashrc

# Install PostgreSQL
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/postgresql.gpg
sudo apt update
sudo apt install -y postgresql-16 postgresql-contrib-16 postgresql-client-16

sudo systemctl start postgresql
sudo systemctl enable postgresql
```

### Step 2: Bash Script for App Setup
Create a `setup.sh` file:
```bash
#!/bin/bash
set -e

echo "Updating Python application on VM..."

APP_DIR="$HOME/SDA-Chatbot-Project"
REPO_URL="https://github.com/Mohammed78vr/SDA-Chatbot-Project.git"
BRANCH="main"
GITHUB_TOKEN=$TOKEN

# Clone or update repo
if [ -d "$APP_DIR" ]; then
    sudo -u azureuser bash -c "cd $APP_DIR && git fetch origin && git reset --hard origin/$BRANCH"
else
    sudo -u azureuser git clone -b "$BRANCH" "https://${GITHUB_TOKEN}@${REPO_URL}" "$APP_DIR"
fi

# Install dependencies
sudo -u azureuser $HOME/miniconda3/envs/project/bin/pip install --upgrade pip
sudo -u azureuser $HOME/miniconda3/envs/project/bin/pip install -r "$APP_DIR/requirements.txt"

# Restart services
sudo systemctl restart backend
sudo systemctl restart frontend
```
Run it using:
```bash
bash setup.sh <PAT_token> <repo_url> <branch> <db_host> <db_name> <db_user> <db_pass>
```

### Step 3: .env File Configuration
```env
OPENAI_API_KEY=your-key
DB_NAME=your-db
DB_USER=your-user
DB_PASSWORD=your-password
DB_HOST=your-host
DB_PORT=5432
AZURE_STORAGE_SAS_URL=...
AZURE_STORAGE_CONTAINER=...
CHROMADB_HOST=your-chromadb-ip
CHROMADB_PORT=8000
```

Enable public access to the database server and add your current IP.

### Step 4: Restart Backend Service
```bash
sudo systemctl restart backend.service
sudo systemctl status backend.service
```

### Step 5: Access the Frontend
Check service status:
```bash
sudo systemctl status frontend.service
```
Access the app using the external IP on port `8501`. Ensure inbound NSG rules allow port 8501.

### Step 6: Prepare CI/CD Scripts
Create a `update_app.sh` script in the repo root:
```bash
#!/bin/bash
set -e

echo "Updating Python application on VM..."

APP_DIR="$HOME/SDA-Chatbot-Project"
REPO_URL="https://github.com/<your_username>/SDA-Chatbot-Project.git"
BRANCH="main"
GITHUB_TOKEN=$TOKEN

if [ -d "$APP_DIR" ]; then
    sudo -u azureuser bash -c "cd $APP_DIR && git fetch origin && git reset --hard origin/$BRANCH"
else
    sudo -u azureuser git clone -b "$BRANCH" "https://${GITHUB_TOKEN}@${REPO_URL}" "$APP_DIR"
fi

sudo -u azureuser $HOME/miniconda3/envs/project/bin/pip install --upgrade pip
sudo -u azureuser $HOME/miniconda3/envs/project/bin/pip install -r "$APP_DIR/requirements.txt"

sudo systemctl restart backend
sudo systemctl restart frontend
```

Then add a GitHub Actions workflow in `.github/workflows/deploy.yml`:
```yaml
name: CI/CD Deployment

on:
  push:
    branches: [main]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Set Up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'

      - name: Install Requirements
        run: pip install -r requirements.txt

      - name: Azure Login
        uses: azure/login@v2.2.0
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      - name: Deploy to Azure VM
        run: |
          az vm extension set \
            --resource-group ${{ secrets.RESOURCE_GROUP }} \
            --vm-name ${{ secrets.VM_NAME }} \
            --name CustomScript \
            --force-update \
            --publisher Microsoft.Azure.Extensions \
            --settings '{"fileUris": []}' \
            --protected-settings '{"commandToExecute": "export GITHUB_TOKEN=${{ secrets.TOKEN }} && sudo -u azureuser bash /home/azureuser/SDA-Chatbot-Project/update_app.sh"}'
```

Add these secrets to your repo:
- `AZURE_CREDENTIALS`
- `RESOURCE_GROUP`
- `VM_NAME`
- `TOKEN`

---

## Conclusion
Stage 6 makes your chatbot ready for real-world deployment, with powerful document-aware capabilities and a modern DevOps pipeline. This setup is highly modular, scalable, and production-grade.
