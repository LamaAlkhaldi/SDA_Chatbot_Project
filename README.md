# Here we are in Stage 2..





In this stage, we upgrade our basic chatbot by integrating FastAPI as the backend framework. Rather than calling the OpenAI API directly from the frontend, all requests are now routed through the FastAPI backend service.



With this architecture, user interactions with the Streamlit frontend are routed to the FastAPI backend, which then communicates with the OpenAI API and returns the generated response.

This separation of concerns allows the frontend to concentrate on user experience and session handling, while the backend manages the business logic and external API requests. Thanks to this modular design, the frontend can be replaced or upgraded with a different technology at any time—without impacting the core functionality of the chatbot.

1-Create a .env file in your project directory and add your OpenAI API key
OPENAI_API_KEY=your-openai-api-key

2-Start the FastAPI server by running
uvicorn backend:app --reload

3-Once the backend is running, start the Streamlit interface:
streamlit run chatbot.py
