# AI-chatbot-Integration
I have an architecture firm and I am looking to have an AI chatbot developed that my clients can use for price quotes. I would like for client to be able to pick from a series of multiple choice answers about their project.  Based on the answers of these questions, once finish, the chatbot will give a rough price quote on the work to be done.  I currently input this data from client into an Excel sheet that generates these quote but would like to convert to AI so that my clients can get quote without speaking to someone.
-------------------
To develop an AI chatbot for your architecture firm that can provide price quotes based on multiple-choice questions, we can break this process down into several key components:

    User Input: A chatbot interface that collects information from clients about their project.
    Price Calculation: The logic behind generating price quotes based on user input.
    Response Generation: The chatbot provides the calculated quote as a response to the user.
    Backend Processing: An AI system or simple rule-based logic that takes user responses and calculates the quote.

We can use a framework like Flask for the backend, Dialogflow or Rasa for the chatbot (Natural Language Processing), and Python for the price calculation logic. You could integrate this with Excel or a database for data handling.

Below is an example of how you can structure this using Python and Flask with a simple backend for the chatbot.
1. Set Up Your Python Environment

First, install the necessary packages using pip:

pip install flask pandas openai

    flask will handle the backend.
    pandas will help in reading the Excel file for price calculations.
    openai will be used for creating the AI chatbot (if you use GPT-3 for the chatbot logic).

2. Simple Backend for Price Calculation

For simplicity, let’s assume your price quotes are based on several factors such as the project type, square footage, location, etc. We will create an Excel sheet (price_quotes.xlsx) to hold price data, which will be used to calculate the price based on user input.
price_quotes.xlsx Example:
Project Type	Square Footage	Price per Sqft
Residential	1000	15
Commercial	3000	25
Mixed-use	2000	20
3. Chatbot API and Flask Backend

from flask import Flask, request, jsonify
import pandas as pd

app = Flask(__name__)

# Load the Excel file containing price data
df = pd.read_excel('price_quotes.xlsx')

# Function to calculate price based on project data
def calculate_quote(project_type, square_footage):
    project_data = df[df['Project Type'] == project_type]
    
    if not project_data.empty:
        price_per_sqft = project_data['Price per Sqft'].values[0]
        total_price = price_per_sqft * square_footage
        return total_price
    else:
        return "Invalid project type"

# Function to handle chatbot responses
def generate_chatbot_response(user_input):
    # Logic to generate chatbot response
    # For example, you could integrate GPT-3 or Dialogflow here
    return f"Thank you for the information. Based on your inputs, here's the quote calculation."

@app.route("/chat", methods=["POST"])
def chat():
    user_input = request.json.get("user_input")

    # Here we would process the user's input to identify project type and size
    # For simplicity, we assume that the input will directly provide those answers
    project_type = request.json.get("project_type")
    square_footage = int(request.json.get("square_footage"))

    if project_type and square_footage:
        quote = calculate_quote(project_type, square_footage)
        return jsonify({"response": f"The estimated price for your project is ${quote:.2f}"})
    else:
        return jsonify({"response": "Please provide valid project details."})

@app.route("/ask", methods=["GET"])
def ask():
    # Here, we provide a list of multiple-choice questions for the chatbot
    questions = [
        "What is the project type? (Residential, Commercial, Mixed-use)",
        "What is the square footage of the project?"
    ]
    return jsonify({"questions": questions})

if __name__ == "__main__":
    app.run(debug=True)

4. Frontend Chatbot Interface

Now, you can create a simple frontend that interacts with the Flask backend. You can use HTML and JavaScript to ask questions and receive responses.
index.html (Frontend Example)

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI Chatbot for Architecture Firm</title>
</head>
<body>
    <h1>Welcome to the Architecture Firm Chatbot</h1>
    <div id="chatbox">
        <div id="chat-log"></div>
        <input type="text" id="user-input" placeholder="Ask your question...">
        <button onclick="sendMessage()">Send</button>
    </div>

    <script>
        // Send message to backend
        function sendMessage() {
            const userInput = document.getElementById("user-input").value;
            fetch('http://127.0.0.1:5000/chat', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify({
                    user_input: userInput,
                    project_type: "Residential",  // Example hardcoded data for now
                    square_footage: 1000         // Example hardcoded data for now
                }),
            })
            .then(response => response.json())
            .then(data => {
                const chatLog = document.getElementById("chat-log");
                chatLog.innerHTML += "<p>" + data.response + "</p>";
                document.getElementById("user-input").value = "";  // clear input
            })
            .catch(error => console.error('Error:', error));
        }

        // Fetch initial questions
        function fetchQuestions() {
            fetch('http://127.0.0.1:5000/ask')
            .then(response => response.json())
            .then(data => {
                const chatLog = document.getElementById("chat-log");
                data.questions.forEach(question => {
                    chatLog.innerHTML += "<p>" + question + "</p>";
                });
            });
        }

        // Initialize chat
        fetchQuestions();
    </script>
</body>
</html>

Explanation:

    Backend (Flask):
        We have a /chat endpoint that accepts user input and computes the price based on the data in an Excel file (price_quotes.xlsx).
        The /ask endpoint returns a list of questions for the user (you can modify this as per your requirement).
    Frontend (HTML/JS):
        The frontend takes user input, sends it to the backend via a POST request, and displays the result (price quote).
        It also asks the user a series of questions about their project (e.g., project type, square footage).

5. Conclusion:

This is a simple example that demonstrates how you can build a basic chatbot that generates price quotes for architecture projects based on user input. You can extend this with more complex logic, such as adding more questions, improving the chatbot’s natural language processing capabilities using models like GPT-3, or even connecting the data to a CRM for customer management.

You can also use Dialogflow or Rasa to build a more sophisticated chatbot if needed. However, this example gives a great starting point for your architecture firm’s needs.
\
Let's break this into two scenarios: one using Dialogflow and the other using Rasa for handling the conversational AI aspect of the chatbot in the architecture firm price quoting system. We will incorporate the use of these tools for more sophisticated responses and natural language processing.
Scenario 1: Using Dialogflow for the AI Chatbot

Dialogflow is a natural language understanding (NLU) platform provided by Google that can process user input and extract meaningful information like entities, intent, and context. Here’s how you can integrate Dialogflow to handle the user interactions in the price quote system.
Steps:

    Create a Dialogflow agent: You first need to create a new agent on Dialogflow.
    Set up intents: Define intents for different types of user inputs, such as asking for a quote, providing details about the project, etc.
    Set up entities: Define entities like "project_type" (Residential, Commercial), "square_footage", etc., that you want to extract from user input.
    Integrate Dialogflow API with your Flask backend: We'll connect the Dialogflow agent to the backend, process user inputs, and return relevant responses (like the price quote).

Example Python Code for Dialogflow Integration:

First, install the necessary packages:

pip install google-cloud-dialogflow
pip install flask

Here’s the Python backend (Flask) to integrate with Dialogflow:

import os
import json
import dialogflow_v2 as dialogflow
from google.oauth2 import service_account
from flask import Flask, request, jsonify
import pandas as pd

app = Flask(__name__)

# Load the Excel file containing price data
df = pd.read_excel('price_quotes.xlsx')

# Google Cloud credentials (download from your Dialogflow console)
os.environ["GOOGLE_APPLICATION_CREDENTIALS"] = "path_to_your_service_account_key.json"

# Function to calculate price based on project data
def calculate_quote(project_type, square_footage):
    project_data = df[df['Project Type'] == project_type]
    
    if not project_data.empty:
        price_per_sqft = project_data['Price per Sqft'].values[0]
        total_price = price_per_sqft * square_footage
        return total_price
    else:
        return "Invalid project type"

# Dialogflow integration to handle user input
def detect_intent_texts(project_id, session_id, text, language_code='en'):
    session_client = dialogflow.SessionsClient()
    session = session_client.session_path(project_id, session_id)

    text_input = dialogflow.types.TextInput(text=text, language_code=language_code)
    query_input = dialogflow.types.QueryInput(text=text_input)

    response = session_client.detect_intent(session=session, query_input=query_input)

    return response.query_result

@app.route("/chat", methods=["POST"])
def chat():
    user_input = request.json.get("user_input")
    session_id = "unique_session_id"  # Can be generated dynamically

    # Detect intent from Dialogflow
    dialogflow_response = detect_intent_texts("your_project_id", session_id, user_input)

    intent = dialogflow_response.intent.display_name
    parameters = dialogflow_response.parameters.fields

    # Extract project type and square footage from Dialogflow parameters
    project_type = parameters.get('project_type', {}).get('stringValue')
    square_footage = parameters.get('square_footage', {}).get('stringValue')

    if project_type and square_footage:
        quote = calculate_quote(project_type, int(square_footage))
        response_text = f"The estimated price for your project is ${quote:.2f}"
    else:
        response_text = "Please provide valid project details."

    return jsonify({"response": response_text, "intent": intent})

if __name__ == "__main__":
    app.run(debug=True)

Dialogflow Setup:

    Create intents in Dialogflow:
        Ask for Quote: When the user requests a quote, the chatbot will trigger an intent like "ask_for_quote".
        Get Project Type: The user will provide a project type (Residential, Commercial), which will be extracted as an entity (project_type).
        Get Square Footage: The user will provide the square footage, which will be extracted as an entity (square_footage).

    Entities in Dialogflow:
        project_type (values: Residential, Commercial, etc.)
        square_footage (user input)

    Dialogflow API Key: You will need a service account key for Dialogflow, which you can get from the Google Cloud Console.

Scenario 2: Using Rasa for the AI Chatbot

Rasa is an open-source framework for building conversational AI applications. Unlike Dialogflow, Rasa is more flexible, and it gives you full control over your AI agent.
Steps:

    Set up Rasa: Install Rasa and create a new Rasa project.
    Define NLU and stories: In Rasa, you'll define the intents, entities, and stories to handle the conversation.
    Connect the backend to Rasa: We'll integrate Rasa with the Flask app and process user input to generate quotes.

Rasa Installation and Setup:

pip install rasa

Create a new Rasa project using:

rasa init --no-prompt

Inside the project, you’ll need to update the following files:

    nlu.yml: Define the intents and entities for Rasa.

version: "2.0"
nlu:
  - intent: request_quote
    examples: |
      - I need a price quote for a project
      - Can you give me a price estimate?
  
  - intent: provide_project_details
    examples: |
      - My project is a Residential building
      - I need a quote for a 1500 square foot space
    entities:
      - project_type: Residential
      - square_footage: 1500

    stories.yml: Define how the conversation will flow.

version: "2.0"
stories:
  - story: request quote
    steps:
      - intent: request_quote
      - action: utter_ask_project_details
      - intent: provide_project_details
      - action: calculate_quote

    domain.yml: Define actions and responses.

version: "2.0"
intents:
  - request_quote
  - provide_project_details

entities:
  - project_type
  - square_footage

actions:
  - calculate_quote

responses:
  utter_ask_project_details:
    - text: "Please provide the project details like the project type and square footage."

  utter_price_quote:
    - text: "The estimated price for your project is ${quote}."

    actions.py: Implement the logic for price calculation.

from rasa_sdk import Action
from rasa_sdk.events import SlotSet
import pandas as pd

# Load price data from Excel
df = pd.read_excel('price_quotes.xlsx')

class ActionCalculateQuote(Action):

    def name(self) -> str:
        return "calculate_quote"

    def run(self, dispatcher, tracker, domain):
        project_type = tracker.get_slot('project_type')
        square_footage = tracker.get_slot('square_footage')

        # Calculate price based on project type and square footage
        project_data = df[df['Project Type'] == project_type]

        if not project_data.empty:
            price_per_sqft = project_data['Price per Sqft'].values[0]
            total_price = price_per_sqft * square_footage
            dispatcher.utter_message(text=f"The estimated price for your project is ${total_price:.2f}")
        else:
            dispatcher.utter_message(text="Sorry, I couldn't find any information for that project type.")

        return []

    Run Rasa and Flask together: To integrate Flask with Rasa, you can use a webhook to send requests to Rasa and get the response.

import requests

@app.route("/chat", methods=["POST"])
def chat():
    user_input = request.json.get("user_input")
    
    # Send the user input to Rasa for processing
    rasa_response = requests.post('http://localhost:5005/webhooks/rest/webhook', json={"message": user_input})
    rasa_data = rasa_response.json()

    # Get response from Rasa
    response_text = rasa_data[0].get('text', "I'm sorry, I didn't get that.")

    return jsonify({"response": response_text})

Conclusion:

    Dialogflow is great for quickly building AI chatbots with pre-built machine learning models. It’s cloud-based, simple to set up, and integrates well with Google’s ecosystem.
    Rasa provides more flexibility and control, allowing you to build a chatbot with more complex flows, custom actions, and offline capabilities.

Both systems allow you to handle user input and integrate it with the price calculation logic, but Dialogflow is more user-friendly, while Rasa gives you more power and flexibility for advanced use cases. Choose based on your needs!
