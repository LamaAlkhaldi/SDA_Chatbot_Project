# Here we are in Stage 3..



In this stage, we improve our chatbot—developed with Streamlit and FastAPI—by implementing chat history storage. Conversations are now saved locally, while session information is recorded in a PostgreSQL database, enabling users to resume their chats seamlessly








We specifically store the chat ID, chat name, and the file path of each conversation in the PostgreSQL database. When the chatbot is reopened, it automatically loads the previous chat history, offering users a more seamless and continuous experience.

By clearly separating concerns—Streamlit for the frontend, FastAPI for backend logic, and PostgreSQL for data persistence—we achieve a modular and scalable architecture. Each layer can be updated, extended, or replaced independently without disrupting the entire system. This clean separation not only improves maintainability but also prepares the project for a smooth transition to cloud deployment in later stages.

Start Here
1-In this stage, we set up a new database named chatbot.
CREATE  DATABASE  chatbot;

2-After that, we define a new table called chats within the PostgreSQL database to keep records of chat history. The structure is outlined as follows:
CREATE TABLE IF NOT EXISTS chats (
    id TEXT PRIMARY KEY,
    name TEXT NOT NULL,
    file_path TEXT NOT NULL,
    last_update TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

Save your OpenAI API key along with your database credentials inside a .env file to securely manage sensitive information.

Your .env file should be structured as follows:

OPENAI_API_KEY=YOUR-OPENAI-API-KEY
DB_NAME=YOUR-DB-NAME
DB_USER=YOUR-DB-USER
DB_PASSWORD=YOUR-DB-PASSWORD
DB_HOST=YOUR-DB-HOST
DB_PORT=YOUR-DB-PORT
Start the Backend

Before running the chatbot, start the FastAPI backend using:

uvicorn backend:app --reload

Start the Frontend

Once the backend is running, launch the Streamlit app with:

streamlit run chatbot.py
