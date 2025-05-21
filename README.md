## 💬 Stage 3: Adding Chat History and Database Integration

In this stage, we enhance our chatbot—built with **Streamlit** and **FastAPI**—by implementing **chat history storage**.  
Conversations are now saved locally, while session details are stored in a **PostgreSQL** database, allowing users to resume their chats seamlessly.

### 🔍 What’s Stored?
We store the following in the PostgreSQL database:
- Chat ID  
- Chat name  
- File path of each conversation  

When the chatbot is reopened, it automatically loads the previous chat history, offering users a smoother and more continuous experience.

### 🧱 Project Architecture
We clearly separate responsibilities:
- **Streamlit** handles the frontend
- **FastAPI** manages backend logic and API interactions
- **PostgreSQL** is used for data persistence

This modular structure improves maintainability and scalability, and prepares the project for cloud deployment in future stages.

---

## 🚀 Start Here

### 1️⃣ Create the Database

Start by creating a PostgreSQL database named `chatbot`:

```sql
CREATE DATABASE chatbot;
```

---

### 2️⃣ Create the `chats` Table

Inside the database, create a table to store chat history:

```sql
CREATE TABLE IF NOT EXISTS chats (
    id TEXT PRIMARY KEY,
    name TEXT NOT NULL,
    file_path TEXT NOT NULL,
    last_update TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

---

### 3️⃣ Configure Environment Variables

Store your OpenAI API key and database credentials securely in a `.env` file:

```env
OPENAI_API_KEY=YOUR-OPENAI-API-KEY
DB_NAME=YOUR-DB-NAME
DB_USER=YOUR-DB-USER
DB_PASSWORD=YOUR-DB-PASSWORD
DB_HOST=YOUR-DB-HOST
DB_PORT=YOUR-DB-PORT
```

---

### 4️⃣ Start the Backend

Run the FastAPI backend before launching the chatbot:

```bash
uvicorn backend:app --reload
```

---

### 5️⃣ Start the Frontend

Once the backend is running, launch the Streamlit frontend:

```bash
streamlit run chatbot.py
```
