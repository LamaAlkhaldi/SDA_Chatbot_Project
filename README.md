# 🚀 Welcome to Stage 8 of the Capstone Project!

## 🧠 Stage 8: RAG Chatbot — Serverless Backend (Continued)

In this stage, the SDA-Chatbot project evolves further by migrating chat history storage from **Azure Blob Storage** to **Azure Cosmos DB**. This enhancement improves the scalability of the system and moves the chat history toward a fully serverless architecture. 📄 💬

---

## 🏗️ Infrastructure Overview

The system architecture includes the following key components:

### 🗄️ Cosmos DB Integration

In this stage, chat history will be stored in **Azure Cosmos DB** instead of files in Blob Storage.

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

> **Note:** This branch demonstrates how to interact with Cosmos DB. Only chat history is stored in Cosmos DB. Students can also migrate all metadata from PostgreSQL to Cosmos DB to make the database fully serverless.

### 🔐 Azure Key Vault

Since the Azure Function needs to connect to Cosmos DB, the following secrets must be stored securely in **Azure Key Vault**:

* `PROJ-COSMOSDB-ENDPOINT`: Cosmos DB endpoint.
* `PROJ-COSMOSDB-KEY`: Cosmos DB access key.
* `PROJ-COSMOSDB-DATABASE`: Cosmos DB database name.
* `PROJ-COSMOSDB-CONTAINER`: Cosmos DB container name.

The Azure Function's URL must also be stored in Key Vault so that the front-end can connect to the backend securely.

### ☁️ Azure Function App

The Azure Function handles backend logic and API requests.

When deploying the application to Azure Functions, make sure to upload the `local.settings.json` configuration to the cloud environment as required by your deployment setup.

The following secret stores the Function App's base URL:

* `PROJ-BASE-ENDPOINT-URL`: The base URL of your Azure Function App.

The URL should follow this format:

```text
https://<your-function-app-name>.azurewebsites.net/api/
```

### 🖥️ Frontend Configuration

The front-end application continues to run on the Azure Virtual Machine (VM) and connects to the Azure Function App.

To allow the front-end to retrieve the Function URL from Azure Key Vault, update the front-end code and add the following variable to the `.env` file on the VM:

```env
KEY_VAULT_NAME=<your-key-vault-name>
```

Make sure the VM has the appropriate permissions to access and retrieve secrets from Azure Key Vault. 🔑

---

## 🔑 Required Azure Key Vault Secrets

The following secrets should be created in your Azure Key Vault:

| Secret Name                    | Description                  |
| ------------------------------ | ---------------------------- |
| `PROJ-DB-NAME`                 | PostgreSQL database name     |
| `PROJ-DB-USER`                 | Database username            |
| `PROJ-DB-PASSWORD`             | Database password            |
| `PROJ-DB-HOST`                 | Database host                |
| `PROJ-DB-PORT`                 | Database port                |
| `PROJ-OPENAI-API-KEY`          | OpenAI API key               |
| `PROJ-AZURE-STORAGE-SAS-URL`   | Azure Storage SAS URL        |
| `PROJ-AZURE-STORAGE-CONTAINER` | Azure Storage container name |
| `PROJ-CHROMADB-HOST`           | ChromaDB server host         |
| `PROJ-CHROMADB-PORT`           | ChromaDB server port         |
| `PROJ-BASE-ENDPOINT-URL`       | Azure Function App base URL  |
| `PROJ-COSMOSDB-ENDPOINT`       | Cosmos DB endpoint           |
| `PROJ-COSMOSDB-KEY`            | Cosmos DB access key         |
| `PROJ-COSMOSDB-DATABASE`       | Cosmos DB database name      |
| `PROJ-COSMOSDB-CONTAINER`      | Cosmos DB container name     |

---

## ⚙️ Backend Services

The Azure VM continues to run **ChromaDB** and **Streamlit**.

### 🧠 ChromaDB Server

Start the ChromaDB server using the following command:

```bash
chroma run --host 0.0.0.0 --path /db_path
```

Replace `/db_path` with the directory where you want to store your ChromaDB data.

For example:

```bash
chroma run --host 0.0.0.0 --path chromadb
```

This starts the ChromaDB server and stores its data in the specified directory.

### 💬 Streamlit Application

Run the Streamlit front-end using the following command:

```bash
streamlit run chatbot.py
```

This launches the chatbot interface, allowing users to interact with the application through the web interface.

---

## 🚀 Stage 8 Summary

In this stage, the project introduces the following enhancements:

* 🗄️ Migrates chat history storage from Blob Storage files to Cosmos DB.
* ☁️ Integrates Cosmos DB with the Azure Function App.
* 🔐 Uses Azure Key Vault to securely manage application secrets.
* 🔗 Connects the front-end to the Azure Function through a stored endpoint URL.
* 🧠 Continues using ChromaDB for vector storage.
* 💬 Runs the Streamlit chatbot on the Azure VM.

These improvements move the SDA-Chatbot toward a more scalable and serverless architecture while maintaining the existing front-end and vector database services. ⚡
