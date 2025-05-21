## 📚 Stage 4: Retrieval-Augmented Generation (RAG) Chatbot

Welcome to **Stage 4**!

In this stage, we upgrade our chatbot with **Retrieval-Augmented Generation (RAG)** capabilities using **Streamlit** and **FastAPI**.  
Users can now **upload PDF files** and ask questions directly related to their content. Behind the scenes, we use **ChromaDB** as a vector store to retrieve the most relevant document context, enhancing the chatbot’s ability to deliver precise and context-aware answers.

---

### 🧠 Key Features

- 🔍 PDF upload support for document-based Q&A
- 📚 ChromaDB vector store for context retrieval
- 🧱 Fully integrated with:
  - Streamlit (Frontend)
  - FastAPI (Backend)
  - PostgreSQL (Data Storage)

> 💡 **Note:** Some advanced LLM concepts are introduced in this stage. Don’t worry if you don’t grasp everything immediately—the main goal is to get the project running smoothly. You can always explore the code and resources further as your understanding grows.

---

## 🚀 Getting Started

### 1️⃣ Create the Database

Start by creating a new PostgreSQL database called `chatbot`:

```sql
CREATE DATABASE chatbot;
```

---

### 2️⃣ Create the `advanced_chats` Table

Add a new table to store metadata for chats with PDF support:

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

### 3️⃣ Configure Environment Variables

Add your OpenAI API key and database credentials to a `.env` file:

```env
OPENAI_API_KEY=YOUR-OPENAI-API-KEY
DB_NAME=YOUR-DB-NAME
DB_USER=YOUR-DB-USER
DB_PASSWORD=YOUR-DB-PASSWORD
DB_HOST=YOUR-DB-HOST
DB_PORT=YOUR-DB-PORT
```

---

### 4️⃣ Install Dependencies

Install ChromaDB and all other required packages via:

```bash
pip install -r requirements.txt
```

---

### 5️⃣ Start ChromaDB Server

To enable retrieval features, start ChromaDB using:

```bash
chroma run --path /db_path
```

Replace `/db_path` with your preferred directory (e.g., `chromadb`).

---

### 6️⃣ Start the Backend Server

Start the FastAPI backend with a custom port to avoid conflicts with ChromaDB (which uses port 8000):

```bash
uvicorn backend:app --reload --port 5000
```

---

### 7️⃣ Launch the Frontend

Finally, start the Streamlit app:

```bash
streamlit run chatbot.py
```
