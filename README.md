# Future 2 Q&A — No-Code RAG Application

A Retrieval-Augmented Generation (RAG) question-answering app that answers Future 2 employees' implementation questions, grounded strictly in three internal documents. Built entirely no-code in n8n Cloud, using open-weight models served through Nebius Token Factory.

Week 2 project for the Mastering Agentic AI Bootcamp. Option 1 (custom use case), Track 1 (no-code, n8n).

## What it does

Employees ask a question in a chat interface. The app retrieves the most relevant passages from the internal documents and uses them to write a grounded answer. If the answer is not in the documents, it refuses instead of guessing.

Goal: 95% grounded accuracy in under 10 seconds per answer.

## Architecture

The workflow has two connected flows on one canvas:

1. **Load Data flow (ingestion):** Form upload to Default Data Loader to Embeddings to Simple Vector Store (insert). Run once per document.
2. **Retriever flow (chat):** Chat Trigger to AI Agent. The agent has a chat model attached and the vector store exposed as a retrieval tool. It retrieves relevant chunks, then answers under a grounding and refusal system prompt.

Both flows share one in-memory vector store and one embedding model.

## Models (served through Nebius Token Factory)

- **Embedding:** Qwen3-Embedding-8B (open-weight, Alibaba), 4,096 dimensions
- **Generation:** Llama 3.3 70B Instruct (open-weight, Meta), temperature ~0.2

Nebius exposes an OpenAI-compatible API, so it connects through n8n's standard OpenAI nodes by setting the base URL to `https://api.tokenfactory.nebius.com/v1`.

## Setup

1. Import `Future2_QnA_workflow.json` into n8n (Workflows to Import from File).
2. Create an n8n credential of type **OpenAI** with your Nebius API key and base URL `https://api.tokenfactory.nebius.com/v1`. Attach it to the Embeddings node and the chat model node.
3. Set the Embeddings model to `Qwen/Qwen3-Embedding-8B` and the chat model to `meta-llama/Llama-3.3-70B-Instruct`.
4. Run the Load Data flow and upload your source documents (one .txt file at a time).
5. Click Open Chat and ask a question.

## Files

- `Future2_QnA_workflow.json` — the exported n8n workflow
- `README.md` — this file

## Notes

- The n8n export does not include the API key, so no secrets are committed.
- The vector store is in-memory, so re-ingest documents at the start of each session.
- Per-document citations and a persistent vector store (Pinecone) are planned next steps.
