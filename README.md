# Stage 6: Enhanced Infrastructure for Document-Aware Chatbot

In Stage 6, the **SDA-Chatbot** project introduces a robust and scalable infrastructure designed to support **Retrieval-Augmented Generation (RAG)** capabilities for document-focused chatbot interactions.

---

## Infrastructure Overview

The system incorporates the following components:

### User Interaction
- Users interact via a **web interface** (Streamlit).
- Users can upload **PDF files** and ask questions specific to the content.

### Secure Azure VM
- Hosts all backend services securely.
- Includes:
  - Subnet with NSG for controlled access.
  - Disk storage for data persistence.
  - Network Interfaces for communication.

### Backend Services
- **Streamlit**: Frontend web interface.
- **FastAPI**: Handles business logic and API routing.

### Data Storage
- **PostgreSQL**: Stores chat history, metadata, and session info.

### Context Retrieval
- **Chroma**: A vector store for retrieving context from PDFs.

### Automation
- **GitHub Actions**: Automates deployment and continuous integration.

---

## Key Benefits

- Support for **context-aware document interaction**.
- Enhanced **response accuracy** using vector-based search.
- Modular and scalable architecture ready for production.

---

## Getting Started

### Step 1: VM Initialization (Custom Data Script)
Use this during VM creation:

```bash
#!/bin/bash
sudo apt update
sudo apt install -y gnupg2 wget

# Install Miniconda
sudo -u azureuser mkdir -p /home/azureuser/miniconda3
sudo -u azureuser wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O /home/azureuser/miniconda3/miniconda.sh
sudo -u azureuser bash /home/azureuser/miniconda3/miniconda.sh -b -u -p /home/azureuser/miniconda3
sudo -u azureuser rm /home/azureuser/miniconda3/miniconda.sh
echo 'export PATH="/home/azureuser/miniconda3/bin:$PATH"' | sudo -u azureuser tee -a /home/azureuser/.bashrc

# Install PostgreSQL
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/postgresql.gpg
sudo apt update
sudo apt install -y postgresql-16 postgresql-contrib-16 postgresql-client-16

sudo systemctl start postgresql
sudo systemctl enable postgresql
```

### Step 2: Bash Script to Run the App
Create `setup.sh`:

```bash
#!/bin/bash
set -e
date
echo "Updating Python application on VM..."

HOME_DIR=$(eval echo ~$USER)
APP_DIR="$HOME_DIR/SDA-Chatbot-Project"
REPO_URL="https://github.com/Mohammed78vr/SDA-Chatbot-Project.git"
BRANCH="main"
GITHUB_TOKEN=$TOKEN  # Passed securely

# Update or clone the repo
if [ -d "$APP_DIR" ]; then
    sudo -u azureuser bash -c "cd $APP_DIR && git fetch origin && git reset --hard origin/$BRANCH"
else
    sudo -u azureuser git clone -b "$BRANCH" "https://${GITHUB_TOKEN}@${REPO_URL}" "$APP_DIR"
fi

# Install dependencies
sudo -u azureuser $HOME_DIR/miniconda3/envs/project/bin/pip install --upgrade pip
sudo -u azureuser $HOME_DIR/miniconda3/envs/project/bin/pip install -r "${APP_DIR}/requirements.txt"

# Restart services
sudo systemctl restart backend
sudo systemctl is-active --quiet backend || echo "Backend failed to start"
sudo systemctl restart frontend
sudo systemctl is-active --quiet frontend || echo "Frontend failed to start"
echo "Python application update completed!"
```

Run it with:
```bash
bash setup.sh <PAT_token> <repo_url> <branch_name> <db_host> <target_db> <db_username> <db_password>
```

### Step 3: Configure Environment Variables
Create a `.env` file:

```env
OPENAI_API_KEY=sk-...
DB_NAME=your-db
DB_USER=your-user
DB_PASSWORD=your-password
DB_HOST=your-db-host
DB_PORT=5432
AZURE_STORAGE_SAS_URL=...
AZURE_STORAGE_CONTAINER=...
CHROMADB_HOST=your-vm-ip
CHROMADB_PORT=8000
```

> ⚠️ Make sure to enable public access and add your IP address in Azure.

### Step 4: Restart Backend
```bash
sudo systemctl restart backend.service
sudo systemctl status backend.service
```

### Step 5: Test Application
```bash
sudo systemctl status frontend.service
```
Use the VM's external IP to access the chatbot.

> 🔒 Allow port `8501` in the NSG for Streamlit access.

---

## Step 6: Prepare GitHub Action for CI/CD
Create `update_app.sh`:

```bash
#!/bin/bash
set -e
date
echo "Updating Python application on VM..."

HOME_DIR=$(eval echo ~$USER)
APP_DIR="$HOME_DIR/SDA-Chatbot-Project"
REPO_URL="https://github.com/<your_github_account>/SDA-Chatbot-Project.git"
BRANCH="main"
GITHUB_TOKEN=$TOKEN

if [ -d "$APP_DIR" ]; then
  sudo -u azureuser bash -c "cd $APP_DIR && git fetch origin && git reset --hard origin/$BRANCH"
else
  sudo -u azureuser git clone -b "$BRANCH" "https://${GITHUB_TOKEN}@${REPO_URL}" "$APP_DIR"
fi

sudo -u azureuser $HOME_DIR/miniconda3/envs/project/bin/pip install --upgrade pip
sudo -u azureuser $HOME_DIR/miniconda3/envs/project/bin/pip install -r "${APP_DIR}/requirements.txt"

sudo systemctl restart backend
sudo systemctl restart frontend
echo "Python application update completed!"
```

Create `.github/workflows/deploy.yml`:

```yaml
name: Python App CI/CD Pipeline with Direct Deployment

on:
  push:
    branches: [ main ]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'
          cache: 'pip'

      - name: Install dependencies
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

> 🧠 Make sure to add these GitHub repository secrets:
> - `AZURE_CREDENTIALS`
> - `RESOURCE_GROUP`
> - `TOKEN` (GitHub PAT)
> - `VM_NAME`

### Step 7: Push and Deploy
Make changes and push to `main`. GitHub Actions will deploy automatically.

---

## ✅ Done!
You now have a cloud-hosted, document-aware RAG chatbot with scalable architecture and CI/CD automation.
