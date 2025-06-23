
# 🤖 Stage 7 - RAG Chatbot (Serverless Backend)

In this stage, we move our backend logic to an **Azure Function App**, converting `backend.py` into a cloud-based serverless function using **Azure Functions V2**.

---

## 🛠️ Key Changes

- Converted backend to **Azure Function V2**.
- Replaced **Stream Response** with **standard HTTP Response** (due to Azure runtime compatibility).
- Updated PDF upload logic: temporary storage path is now `/tmp` (the only writable path in Azure Functions).

---

## 🗃️ Database Schema

We are still using the `advanced_chats` table:

```sql
CREATE TABLE IF NOT EXISTS advanced_chats (
    id TEXT PRIMARY KEY,
    name TEXT NOT NULL,
    file_path TEXT NOT NULL,
    last_update TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    pdf_path TEXT,
    pdf_name TEXT,
    pdf_uuid TEXT
);
```

---

## 🔐 Configuration - `local.settings.json`

The Azure Key Vault name should be stored in the local Azure Function settings file:

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "KEY_VAULT_NAME": "<YOUR-KEY-VAULT>"
  }
}
```

> ⚠️ Don’t forget to upload `local.settings.json` to Azure during deployment.

---

## 🔑 Required Secrets in Azure Key Vault

The following secrets must be created:

- `PROJ-DB-NAME`
- `PROJ-DB-USER`
- `PROJ-DB-PASSWORD`
- `PROJ-DB-HOST`
- `PROJ-DB-PORT`
- `PROJ-OPENAI-API-KEY`
- `PROJ-AZURE-STORAGE-SAS-URL`
- `PROJ-AZURE-STORAGE-CONTAINER`
- `PROJ-CHROMADB-HOST`
- `PROJ-CHROMADB-PORT`
- `PROJ-BASE-ENDPOINT-URL` → Example: `https://<your-function-app-name>.azurewebsites.net/api/`

---

## 🌐 Frontend Configuration

To allow the frontend to connect with Azure Functions:

- Store the Function App URL in Azure Key Vault.
- Add `KEY_VAULT_NAME` to the `.env` file on the VM.
- Make sure the VM has permission to access Key Vault.

---

## ⚙️ Running Services on VM

### Start ChromaDB:
```bash
chroma run --host 0.0.0.0 --path chromadb
```

### Start Streamlit frontend:
```bash
streamlit run chatbot.py
```
