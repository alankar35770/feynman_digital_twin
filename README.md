# Richard Feynman Digital Twin

This is a Digital Twin of Richard Feynman built using RAG, Gemini 2.5 Flash, FAISS, and a simple memory system.

The idea is simple: instead of asking a general chatbot physics questions, I wanted to create something that answers using Feynman's own lecture material and tries to explain concepts in a way similar to how he taught them.

The system retrieves relevant chunks from Feynman's lectures, sends them along with conversation history to Gemini, and generates a response in a Feynman inspired style.

## What it can do

* Answer physics questions using retrieved Feynman lecture content
* Maintain a Feynman like teaching style
* Remember previous messages during a conversation
* Save conversations and reload them later
* Show which chunks were retrieved for a response
* Run as an interactive chat application

## Dataset

The knowledge base was built primarily from:
Richard Feynman's lectures from https://www.feynmanlectures.caltech.edu/

The lecture text was collected and combined into a corpus before chunking and embedding.

## Data Flow

1. Load the lecture corpus.
2. Split the text into chunks.
3. Generate embeddings using BAAI/bge-small-en-v1.5.
4. Store embeddings in a FAISS index.
5. Retrieve the most relevant chunks for a user question.
6. Send retrieved context, memory, and persona instructions to Gemini 2.5 Flash.
7. Return the answer.

Memory is stored in a JSON file so conversations can survive notebook restarts.

## Installation

Install the required packages:
pip install sentence-transformers
pip install faiss-cpu
pip install langchain-text-splitters
pip install google-genai


## Running the project

Open the notebook and run the cells in order.

Make sure you:

1. Add your Gemini API key.
2. Mount Google Drive.
3. Build or load the corpus.
4. Build embeddings and FAISS index.
5. Run the chat function.

Start the interactive demo with:
chat()

## Files

Typical files generated during execution:


feynman_corpus.txt
feynman_chunks.pkl
feynman_embeddings.pkl
feynman_faiss.index
feynman_memory.json


## Memory

Two types of memory are implemented.

### Short-term memory

Stores previous conversation turns during the current chat session.

### Long-term memory

Stores conversation history in:
feynman_memory.json

The file is loaded again when the notebook starts.

## Known Issues

* Retrieval quality depends heavily on the amount of lecture material available.
* Follow-up questions sometimes retrieve irrelevant chunks.
* The personality is prompt-based, so it is not a perfect simulation of Feynman.
* The corpus was assembled manually from lecture text, so there may be formatting inconsistencies.
* If Gemini servers are under heavy load, API requests may occasionally fail and need to be retried.

## Future Improvements

* Add more lecture chapters and transcripts.
* Improve retrieval quality using reranking.
* Add source citations with direct references.
* Add speech input and voice output.
* Build a web interface instead of using a notebook.
* Adding additional supporting material such as quotes and biographical information

## Example Questions

* What is probability?
* Explain entropy.
* What is energy?
* Why do physicists use models?
* Explain uncertainty in simple terms.
* How do atoms work?
