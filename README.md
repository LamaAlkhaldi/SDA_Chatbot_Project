
# 🚀 Welcome to Stage 6 of the Capstone Project!

## 🧠 Stage 6: Enhanced Infrastructure for a Document-Aware Chatbot

In this stage, the **SDA-Chatbot** project evolves into a robust and scalable system that integrates advanced technologies to enable **Retrieval-Augmented Generation (RAG)**. This enhancement allows the chatbot to understand and respond to **document-specific queries** using uploaded PDFs. 📄💬

---

## 🏗️ Infrastructure Overview

The system architecture includes the following key components:

### 🧑‍💻 User Interaction
- Users communicate with the chatbot via a **web interface**.
- Supports both general chat and document-specific queries through **PDF uploads**.

### ☁️ Secure Azure VM
The Azure Virtual Machine (VM) forms the backbone of the infrastructure:

- **Subnet with NSG (Network Security Group)**: Controls access securely.
- **Disk Storage**: Ensures persistent data storage.
- **Network Interfaces**: Enables external communication with the VM.

### ⚙️ Backend Services
- The VM runs:
  - **Streamlit** for the user interface.
  - **FastAPI** for backend logic and API handling.
  
This ensures a fast and interactive user experience. ⚡

### 🛢️ PostgreSQL Database
- All chat history and related metadata are stored securely using **PostgreSQL**. 🗄️

### 📚 Chroma for Context Retrieval
- **Chroma**, a vector store, indexes and retrieves relevant parts of uploaded PDFs.
- It enhances the chatbot’s ability to provide **context-aware answers**. 🧩

### 🔁 GitHub Actions for CI/CD
- **GitHub Actions** automate deployments and updates, enabling **continuous integration and delivery**. 🚀

---

## 🌟 Key Benefits of This Stage

✅ Query uploaded PDFs in a meaningful way.  
✅ Accurate responses powered by context-aware retrieval.  
✅ Scalable, modular architecture ready for production environments.  

> ℹ️ **Note:** This stage demonstrates a real-world RAG implementation. Don’t worry if some concepts seem complex—just focus on running the project and explore more as needed!

---

## 🧠 Under the Hood

Chroma retrieves relevant PDF content to enhance the chatbot's answers. This bridges the gap between casual chatting and document-level understanding.

This layer integrates **seamlessly** with:
- Streamlit (UI)
- FastAPI (Backend)
- PostgreSQL (Data)

---

## 🛠️ How to Get Started

### 🔑 Step 1: Grant Key Vault Access to the Azure VM

#### 1️⃣ Enable System Assigned Identity on VM
1. Go to your Azure VM.
2. Click **Security > Identity**.
3. Under the *System assigned* tab, set the status to **ON**, then click **Save**.

#### 2️⃣ Assign Key Vault Access
1. Go to **Access control (IAM)** in your VM.
2. Click **+ Add > Add role assignment**.
3. In the *Role* tab, search for and select **Key Vault Secrets User**.
4. In the *Members* tab:
   - Choose **Managed identity**.
   - Click **+ Select members**.
   - Choose your **subscription** and select your **VM**.
   - Click **Select**.
5. Click **Review + assign** to complete.

You should now see the role assigned under **Key Vault Secrets User** in the *Role assignments* tab. ✅
