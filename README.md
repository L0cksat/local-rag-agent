# Local AI Agent & RAG Pipeline (Erasia Lore Engine)

![n8n](https://img.shields.io/badge/n8n-FF6D5A?style=for-the-badge&logo=n8n&logoColor=white)
![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)
![Qdrant](https://img.shields.io/badge/Qdrant-FD5750?style=for-the-badge&logo=qdrant&logoColor=white)
![Ollama](https://img.shields.io/badge/Ollama-FFFFFF?style=for-the-badge&logo=ollama&logoColor=black)
![Telegram API](https://img.shields.io/badge/Telegram-2CA5E0?style=for-the-badge&logo=telegram&logoColor=white)
![Obsidian](https://img.shields.io/badge/Obsidian-483699?style=for-the-badge&logo=obsidian&logoColor=white)

A 100% locally hosted, containerized Retrieval-Augmented Generation (RAG) architecture. This project orchestrates autonomous AI agents to query a local vector database and deliver highly contextual responses through a Telegram bot interface, without relying on external LLM APIs.

## The Objective

As a Backend Engineer, I built this pipeline to test and solidify my skills in **AI orchestration, data persistence, and infrastructure deployment**. 

Rather than building a generic wrapper around OpenAI's API, I engineered a self-hosted environment to tackle the real challenges of Agentic AI: prompt engineering for tool reliability, managing context windows, debugging agent hallucinations, and designing structured workflows. The dataset driving this engine is my personal Obsidian vault, containing hundreds of Markdown files detailing the lore, factions, and NPCs of a custom D&D campaign setting ("The World of Erasia").

## Live Demonstration

[**► Watch the 60-second architecture & execution demo here**](LINK_A_TU_VIDEO_AQUI)

*The demo shows the Telegram interface interacting with the AI agent, running in parallel with the n8n execution canvas highlighting the real-time tool querying and vector retrieval.*

## Screenshot


## Architecture & Workflow

The system is fully containerized using `docker-compose` and is split into two primary workflows orchestrated by n8n:

### 1. Data Ingestion Pipeline (The Brain)
*   **Source:** Reads raw Markdown files directly from a local Obsidian vault.
*   **Processing:** Chunks the text and generates vector embeddings using `nomic-embed-text:latest` via Ollama.
*   **Storage:** Upserts the vectorized data into a persistent **Qdrant** database collection (`erasia`).

### 2. Retrieval & Agent Execution Pipeline (The Interface)
*   **Trigger:** Receives natural language queries via the Telegram API (routed through an ngrok tunnel to the local n8n instance).
*   **Agent Execution:** An n8n AI Agent acts as the controller, equipped with custom system instructions (persona: Cecilia).
*   **Tool Usage:** The agent dynamically decides when to query the `erasia_lore_vault` tool (Qdrant Vector Store).
*   **Generation:** Uses `qwen2.5:7b` (via Ollama) to synthesize the retrieved context into a clean, Markdown-formatted response.
*   **Delivery:** Formats and chunks the output to comply with Telegram's message limits before dispatching the final payload.

## Tech Stack & Models

*   **Orchestration:** n8n (Self-hosted via Docker)
*   **Vector Database:** Qdrant (Self-hosted via Docker)
*   **LLM Provider:** Ollama (Local execution)
*   **Chat Model:** `qwen2.5:7b` (Optimized for instruction following and tool usage)
*   **Embedding Model:** `nomic-embed-text:latest`
*   **Integration:** Telegram API, ngrok

## How to Run (Local Deployment)

*Note: Requires Docker, Docker Compose, and Ollama installed on the host machine.*

1. Clone this repository.
2. Start the infrastructure:
   ```bash
   docker-compose up -d
   ```
3. Pull the required models in Ollama:
```bash
ollama run qwen2.5:7b
ollama pull nomic-embed-text:latest
```
4. Import the `Erasia_Telegram_Agent.json` workflow into your local n8n instance.
5. Update the credentials for Telegram, Qdrant, and Ollama within the n8n nodes.
6. Trigger the ingestion pipeline manually to populate Qdrant, then activate the webhook to start chatting.