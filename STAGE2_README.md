## 🚀 Stage 2: Introducing FastAPI Backend Integration

Here we are in **Stage 2**.

In this stage, we upgrade our basic chatbot by integrating **FastAPI** as the backend framework.  
Instead of calling the OpenAI API directly from the frontend, all requests are now routed through the FastAPI backend service.

### ⚙️ How It Works

With this architecture:
- The **Streamlit frontend** handles user interaction and sends requests.
- The **FastAPI backend** processes these requests, communicates with the **OpenAI API**, and sends the response back to the frontend.

### 🌟 Why This Matters

This **separation of concerns** brings several benefits:
- The frontend focuses on user experience and session handling.
- The backend handles business logic and external API requests.
- The modular structure allows you to **upgrade or replace** the frontend without affecting core chatbot functionality.

---

## 🧪 Getting Started

### 1️⃣ Configure Your API Key

Create a `.env` file in your project directory and add your OpenAI API key:

```env
OPENAI_API_KEY=your-openai-api-key
```

---

### 2️⃣ Start the FastAPI Backend

Run the FastAPI server using:

```bash
uvicorn backend:app --reload
```

---

### 3️⃣ Launch the Streamlit Frontend

Once the backend is running, start the frontend interface with:

```bash
streamlit run chatbot.py
```
