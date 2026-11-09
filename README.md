# AI Invoice Voice Agent

### AI-Powered Invoice Management Assistant Using ElevenLabs, FastAPI, React, and RAG

An intelligent voice-based invoice support assistant that helps customers retrieve invoice details, check payment statuses, view due dates, and understand billing policies through natural conversations.

The project integrates **ElevenLabs Conversational AI** with a locally hosted **FastAPI backend**, **SQLite database**, and **ChromaDB Retrieval-Augmented Generation (RAG)** system.

It demonstrates how conversational AI agents can use custom tools to retrieve structured financial information and search knowledge documents.

## Features

### 1. AI Voice Assistant
- Natural voice conversations powered by ElevenLabs.
- Voice-based customer invoice inquiries.
- Supports spoken customer IDs and invoice numbers.
- Professional and conversational responses.

### 2. Invoice Management
- Retrieve invoice information using invoice ID and customer ID.
- Check invoice payment status.
- Retrieve invoice amounts and due dates.
- Support simplified numeric customer IDs.
- Retrieve invoice information directly from SQLite.

### 3. Retrieval-Augmented Generation (RAG)
- Local RAG implementation using ChromaDB.
- Sentence Transformers for document embeddings.
- Semantic search across billing policy documents.
- Retrieve information about payment terms, refund rules, and FAQs.
- Provide relevant knowledge snippets to the AI agent.

### 4. Custom AI Agent Tools

**get_invoice**
- Retrieves invoice information from SQLite.
- Accepts invoice ID and customer ID.
- Returns invoice amount, currency, status, and due date.

**search_knowledge**
- Searches locally stored knowledge documents.
- Uses semantic similarity to retrieve relevant passages.
- Returns matching text and document sources.

### 5. Local Data Storage
- SQLite for invoice records and audit logs.
- ChromaDB for vector embeddings.
- Local filesystem for billing documents.
- Optional local microphone recording endpoint.

**Note:** ElevenLabs performs voice AI processing in the cloud. The database and RAG system run locally, but information returned to ElevenLabs tools may be transmitted to its services.

## Technology Stack

| Component | Technology |
|---|---|
| Frontend | React.js, Vite |
| Voice AI | ElevenLabs Agents |
| Backend | Python, FastAPI |
| Database | SQLite |
| Vector Database | ChromaDB |
| Embedding Model | all-MiniLM-L6-v2 |
| Machine Learning | Sentence Transformers |
| API Communication | REST API |
| Local Storage | Filesystem |
| Version Control | Git, GitHub |

## System Architecture

```text
Customer
    |
    v
React Web Application
    |
    v
ElevenLabs Conversational AI
    |
    v
Client Tool Invocation
    |
    v
React Client Tool Handlers
    |
    v
Local FastAPI Backend
    |
    +------------------------+
    |                        |
    v                        v
SQLite Database          ChromaDB RAG
    |                        |
    v                        v
Invoice Details          Billing Policies
    |                        |
    +-----------+------------+
                |
                v
         Tool Response
                |
                v
       ElevenLabs AI Agent
                |
                v
       Customer Voice Reply
```

## Project Structure

```text
AI-Invoice-Voice-Agent/
|
|-- backend/
|   |-- main.py
|   |-- ingest.py
|   |-- seed_demo.py
|   |-- requirements.txt
|   |
|   |-- data/
|       |-- docs/
|           |-- payment_policy.txt
|           |-- refund_policy.txt
|           |-- other_policy_documents.txt
|
|-- frontend/
|   |-- src/
|   |   |-- main.jsx
|   |-- index.html
|   |-- package.json
|   |-- .env.example
|
|-- .gitignore
|-- README.md
```

The exact number and names of policy documents depend on the synthetic dataset included with the project.

Generated database files, embeddings, recordings, dependencies, and secrets are excluded from version control.

## Installation and Setup

### Prerequisites

Install the following:

- Python 3.10 or newer, compatible with the selected dependencies.
- Node.js 20.19+ or 22.12+.
- Git.
- An ElevenLabs account and configured conversational AI agent.
- An internet connection for ElevenLabs voice conversations and initial model downloads.

### Step 1: Clone the Repository

```bash
git clone https://github.com/preetam-11/Voice-agent.git
cd Voice-agent
```

### Step 2: Setup the Backend

Navigate to the backend directory:

```bash
cd backend
```

Create a virtual environment:

```bash
python -m venv .venv
```

Activate it on Windows PowerShell:

```powershell
.\.venv\Scripts\Activate.ps1
```

Install Python dependencies:

```bash
pip install -r requirements.txt
```

### Step 3: Prepare the Demo Database

The application initializes the SQLite database automatically with a default demonstration invoice.

If the repository includes the synthetic-data seed script, run:

```bash
python seed_demo.py
```

This creates additional fictional customer and invoice records for testing.

### Step 4: Initialize RAG

Add billing policies, refund rules, and FAQ text documents to:

```text
backend/data/docs/
```

Generate and store document embeddings:

```bash
python ingest.py
```

The embedding model is downloaded on first use and cached for subsequent runs.

The vector database is stored locally using ChromaDB.

### Step 5: Start the Backend Server

```bash
python -m uvicorn main:app --host 127.0.0.1 --port 8000 --reload
```

FastAPI documentation:

http://127.0.0.1:8000/docs

Health-check endpoint:

http://127.0.0.1:8000/health

### Step 6: Setup the Frontend

Open a new terminal and navigate to the frontend directory:

```bash
cd frontend
```

Install dependencies:

```bash
npm install
```

Create a `.env` file inside the `frontend` folder:

```env
VITE_ELEVENLABS_AGENT_ID=YOUR_AGENT_ID
```

Replace `YOUR_AGENT_ID` with your ElevenLabs Agent ID.

Do not put secret ElevenLabs API keys in frontend environment variables.

Start the React application:

```bash
npm run dev
```

Open:

http://127.0.0.1:5173

## ElevenLabs Agent Configuration

Create an ElevenLabs conversational agent named **Invoice AI Assistant**.

Configure two Client Tools in the ElevenLabs dashboard.

### Tool 1: get_invoice

**Tool type:** Client

**Parameters:**

| Parameter | Type | Required |
|---|---|---|
| invoice_id | String | Yes |
| customer_id | String | Yes |

**Purpose:** Retrieve invoice information using the local FastAPI backend.

### Tool 2: search_knowledge

**Tool type:** Client

**Parameters:**

| Parameter | Type | Required |
|---|---|---|
| question | String | Yes |

**Purpose:** Retrieve billing policy information using the local RAG system.

Enable **Wait for response** for both tools.

Publish the agent after configuring the tools.

The React application must register handlers with matching Client Tool names.

## API Endpoints

| Method | Endpoint | Description |
|---|---|---|
| GET | /health | Verify backend status |
| POST | /tools/get_invoice | Retrieve invoice information |
| POST | /tools/search_knowledge | Search billing documents |
| POST | /recordings | Save supported audio uploads locally |

### Example: Invoice Lookup

**Request**

```json
{
  "invoice_id": "INV-1001",
  "customer_id": "1001"
}
```

**Example response**

```json
{
  "id": "INV-1001",
  "customer_id": "1001",
  "currency": "INR",
  "status": "unpaid",
  "due_date": "2026-12-15",
  "amount": "1250.00"
}
```

This response uses the project's fictional demonstration invoice.

### Example: RAG Search

**Request**

```json
{
  "question": "What are the payment terms?"
}
```

**Response structure**

```json
{
  "question": "What are the payment terms?",
  "matches": [
    {
      "source": "payment_policy.txt",
      "text": "Relevant passage from the indexed policy document.",
      "distance": 0.25
    }
  ],
  "total_matches": 1
}
```

The response shown is illustrative. Actual matches and similarity distances depend on the indexed documents.

## Voice Interaction Example

**Customer:**

"Hello, I want to check my invoice number one zero zero one. My customer ID is one zero zero one."

**AI Agent:**

Calls `get_invoice` through the React Client Tool handler.

**Example AI response:**

"Your invoice INV-1001 is for 1,250 Indian rupees. The current status is unpaid, and the due date is December 15, 2026."

### RAG Interaction Example

**Customer:**

"What are your payment terms?"

**AI Agent:**

Calls `search_knowledge`, retrieves relevant policy passages from ChromaDB, and explains the available information.

## Security and Privacy

This project is a local-first demonstration intended for educational and portfolio purposes.

- All included customer records should be synthetic.
- API keys and private configuration files must not be committed.
- Customer IDs are lookup identifiers, not authentication credentials.
- Real invoice disclosure requires authenticated and authorized user access.
- Local recordings require appropriate user consent.
- Financial data should not be used in production without additional security controls.
- ElevenLabs cloud services handle live voice processing.

The current demonstration is not intended for unrestricted public production use.

## Future Enhancements

- Secure customer authentication and authorization.
- Customer dashboard for invoice management.
- Invoice PDF generation.
- Payment reminder workflows.
- Invoice upload and extraction.
- OCR for scanned invoice documents.
- Multi-language customer support.
- Locally managed conversation transcripts and audit logs.
- Improved knowledge retrieval and answer evaluation.
- Fully local speech-to-text and text-to-speech alternatives.

## Project Status

**Working local prototype**

- React frontend implemented.
- FastAPI backend implemented.
- SQLite invoice lookup implemented.
- ChromaDB RAG implemented.
- ElevenLabs Client Tool integration implemented.
- Synthetic data testing supported.

Live voice conversations require an active ElevenLabs account with sufficient usage credits.

## Author

**Sai Preetam**

GitHub: https://github.com/preetam-11

## Disclaimer

This project is a demonstration of AI-assisted invoice support and retrieval-augmented generation. It uses fictional financial data and is not a production-ready payment processing or financial services platform.
