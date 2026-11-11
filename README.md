# Express AI Agent with LangChain and Gemini

This project is a minimal Express-based AI agent built with LangChain, LangGraph, and Google Gemini. It exposes a `/chat` endpoint that accepts a user prompt and lets the model decide whether to call tools before returning a final response.

## What this project does

The application:

- starts an Express server
- reads JSON requests
- sends the user prompt to a Gemini model
- invokes custom tools when needed
- loops through a LangGraph agent flow until the final answer is produced

## Main components

### Server
The app uses Express to run a simple API server.

- Port: `process.env.PORT || 5000`
- Endpoint: `POST /chat`

### AI model
The project uses:

- `@langchain/google`
- `ChatGoogle("gemini-2.5-flash")`

This model is bound to a set of custom tools so it can perform structured tool calls.

### Tools
Two custom tools are registered:

1. `email_send_tool`
   - Sends an email
   - Parameters:
     - `email` (string)
     - `content` (string)

2. `addition_tool`
   - Performs a simple addition example
   - Parameters:
     - `a` (number)
     - `b` (number)

The tool list is passed into the model and also into a `ToolNode`, which allows the agent to execute called tools and continue the workflow.

### Agent flow
The app uses a `StateGraph` from LangGraph with these nodes:

- `agent`
- `tool_node`

The agent receives the conversation state, calls the LLM, and then checks whether the last message contains tool calls.

- If tool calls exist, it routes to `tool_node`
- Otherwise, it ends the graph

## Environment setup

Create a `.env` file in the same folder and add the required environment variables.

Example:

```env
PORT=5000
GOOGLE_API_KEY=your_google_api_key_here
```

> The exact environment variable name may vary based on your `@langchain/google` setup, but `GOOGLE_API_KEY` is the common value used for Gemini access.

## Installation

Install the required dependencies:

```bash
npm install express dotenv @langchain/google @langchain/tavily langchain @langchain/langgraph zod
```

## Run the app

```bash
node index.js
```

Then the server will start on the configured port (default: `5000`).

## API usage

### Request

```http
POST /chat
Content-Type: application/json
```

```json
{
  "prompt": "Send an email to someone@example.com saying hello"
}
```

### Response

The server responds with the final message content from the agent as JSON.

Example:

```json
"email sent successfully to someone@example.com with content hello"
```

## Notes

This project is a simple example of a tool-using AI agent in Node.js. It demonstrates:

- Express API setup
- LangChain and LangGraph integration
- Gemini model usage
- Tool calling with structured input schemas
- Stateful agent workflow orchestration

## File structure

```text
level2/
├── index.js
├── README.md
```

## Example use cases

- Ask the agent to send an email
- Ask the agent to compute a sum using the addition tool
- Extend the system with more tools for APIs, search, or databases

## Important

This is a lightweight demo project intended for learning and experimentation. It is not a production-ready app and should be hardened before deployment in a real environment.
