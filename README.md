# Here we are in Stage 3..


# Create a README.md file with the provided content
readme_content = """\
## 💬 Stage 2: Adding Chat History and Database Integration

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
