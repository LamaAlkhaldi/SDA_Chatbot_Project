# 🚀 Welcome to Stage 8 of the Capstone Project!

## 🧠 Stage 8: RAG Chatbot — Serverless Backend (Continued)

In this stage, the SDA-Chatbot project evolves by moving chat history from files stored in **Azure Blob Storage** to **Azure Cosmos DB**. This enhancement improves the system's scalability and supports a more serverless architecture. 📄 💬

---

## 🏗️ Infrastructure Overview

The system architecture includes the following key components:

### 👤 User Interaction

* Users communicate with the chatbot through a web interface.
* Supports general chat and document-specific queries.
* Chat history is stored in Cosmos DB.

### ☁️ Azure Function App

The Azure Function App handles backend logic and API requests.

* Connects to Azure Cosmos DB to store chat history.
* Uses Azure Key Vault to retrieve configuration secrets.
* Provides an API endpoint for the front-end application.

When deploying to Azure Functions, make sure to upload the `local.settings.json` configuration to the cloud environment as required by your deployment setup.

### 🗄️ Cosmos DB Database

In this stage, chat history is migrated from Blob Storage files to **Azure Cosmos DB**.

To update the database, remove the `file_path` column from the `advanced_chats` table.

You can either modify the existing table or create a new table called `advanced_chats_new` using the following SQL statement:

```sql
CREATE TABLE IF NOT EXISTS advanced_chats (
    id TEXT PRIMARY KEY,
    name TEXT NOT NULL,
    -- file_path TEXT NOT NULL,
    last_update TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    pdf_path TEXT,
    pdf_name TEXT,
    pdf_uuid TEXT
);
```

> **Note:** This branch demonstrates how to interact with Cosmos DB, so only chat history is stored in Cosmos DB. Students can also migrate all metadata from PostgreSQL to Cosmos DB to make the database fully serverless.

### 🔐 Azure Key Vault

Azure Key Vault securely stores the secrets required by the Azure Function and front-end application.

The following Cosmos DB secrets must be created:

* `PROJ-COSMOSDB-ENDPOINT`
* `PROJ-COSMOSDB-KEY`
* `PROJ-COSMOSDB-DATABASE`
* `PROJ-COSMOSDB-CONTAINER`

The Function App URL must also be stored in Azure Key Vault so the front-end can retrieve it securely.

### 🖥️ Frontend Configuration

The front-end continues to run on the Azure Virtual Machine (VM) and connects to the Azure Function App.

To allow the front-end to retrieve secrets from Azure Key Vault, update the front-end code and add the following variable to the `.env` file on the VM:

```env
KEY_VAULT_NAME=<your-key-vault-name>
```

Make sure the VM has the appropriate permissions to access and retrieve secrets from Azure Key Vault. 🔑

---

## 🔑 Required Azure Key Vault Secrets

The following secrets should be created in your Azure Key Vault:

* `PROJ-DB-NAME`
* `PROJ-DB-USER`
* `PROJ-DB-PASSWORD`
* `PROJ-DB-HOST`
* `PROJ-DB-PORT`
* `PROJ-OPENAI-API-KEY`
* `PROJ-AZURE-STORAGE-SAS-URL`
* `PROJ-AZURE-STORAGE-CONTAINER`
* `PROJ-CHROMADB-HOST`
* `PROJ-CHROMADB-PORT`
* `PROJ-BASE-ENDPOINT-URL`
* `PROJ-COSMOSDB-ENDPOINT`
* `PROJ-COSMOSDB-KEY`
* `PROJ-COSMOSDB-DATABASE`
* `PROJ-COSMOSDB-CONTAINER`

The value of `PROJ-BASE-ENDPOINT-URL` should follow this format:

```text
https://<your-function-app-name>.azurewebsites.net/api/
```

---

## ⚙️ Backend Services

The Azure VM continues to run **ChromaDB** and **Streamlit**.

### 🧠 ChromaDB Server

Start the ChromaDB server using the following command:

```bash
chroma run --host 0.0.0.0 --path /db_path
```

Replace `/db_path` with the directory where you want to store your data.

For example:

```bash
chroma run --host 0.0.0.0 --path chromadb
```

This starts the ChromaDB server and stores its data in the specified directory.

### 💬 Streamlit Application

Run the Streamlit application using the following command:

```bash
streamlit run chatbot.py
```

This launches the chatbot interface, allowing users to interact with the application through the web interface.

---

## 🚀 Stage 8 Summary

In this stage, the project introduces the following enhancements:

* 🗄️ Migrates chat history from Blob Storage files to Cosmos DB.
* ☁️ Integrates Cosmos DB with the Azure Function App.
* 🔐 Secures application secrets using Azure Key Vault.
* 🔗 Connects the front-end to the Azure Function App.
* 🧠 Continues running ChromaDB on the Azure VM.
* 💬 Uses Streamlit to provide the chatbot interface.

These improvements enhance the scalability of the SDA-Chatbot project and move it toward a serverless backend architecture. ⚡
