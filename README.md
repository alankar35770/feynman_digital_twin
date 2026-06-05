# Richard Feynman Digital Twin
This is a Digital Twin of Richard Feynman built using RAG, Gemini 2.5 Flash, FAISS, and a Gradio web interface with a built-in memory dashboard.
The idea is the same as before: instead of asking a general chatbot physics questions, I wanted to create something that answers using Feynman's own lecture material and tries to explain concepts the way he actually taught them. What's new this time is the Gradio interface, which makes the whole thing feel much more like a real application rather than a notebook you run cell by cell.

## What it can do
1. Answer physics questions using retrieved Feynman lecture content
2. Maintain a Feynman-like teaching style throughout the conversation
3. Remember previous messages during a session using short-term memory
4. Persist conversations across notebook restarts using a JSON memory file
5. Show which text chunks were retrieved for any given response
6. Display full conversation history in a live memory dashboard tab
7. Reset the conversation and start fresh at any time
8. Run as an interactive Gradio chat application with a browser UI

## Dataset
The knowledge base was built primarily from Richard Feynman's lectures available at https://www.feynmanlectures.caltech.edu/
The lecture text was collected and combined into a single corpus before chunking and embedding. The corpus covers topics ranging from conservation laws and quantum mechanics to the nature of probability and the structure of atoms.

## Data Flow
1. Load the lecture corpus from Google Drive.
2. Split the text into overlapping chunks using LangChain's text splitter.
Generate embeddings for each chunk using BAAI/bge-small-en-v1.5.
Store all embeddings in a FAISS index for fast similarity search.
At query time, retrieve the top k most relevant chunks for the user's question.
Format retrieved context together with conversation history and persona instructions.
Send the full prompt to Gemini 2.5 Flash and return the response.
Save the conversation turn to memory so follow-up questions work properly.

Memory is stored in a JSON file on Google Drive so it survives notebook restarts.

## Installation
Install the required packages:
pip install sentence-transformers
pip install faiss-cpu
pip install langchain-text-splitters
pip install google-genai
pip install gradio
pip install pandas
Running the Project
Open the notebook and run the cells in order. Make sure you:

Add your Gemini API key where indicated.
Mount Google Drive so the corpus and memory files can be read and saved.
Build or load the corpus from the lecture text files.
Build the embeddings and FAISS index (or load them if already saved).
Run the Gradio app cell to launch the interface.

The app launches with two tabs. The Chat tab is the main interface where you talk to Feynman. The Memory Dashboard tab shows a table of all stored conversation turns and has a refresh button to update it live.
To launch the app:
pythondemo.launch(share=True, debug=True)
This will give you a public shareable link that works outside of Colab.

## Files
Typical files generated during execution:
feynman_corpus.txt
feynman_chunks.pkl
feynman_embeddings.pkl
feynman_faiss.index
feynman_memory.json
All of these are saved to and loaded from Google Drive so nothing is lost between sessions.
Memory
Two types of memory are implemented.
Short-term memory stores previous conversation turns in the current session as a list of user and assistant pairs. This is what gets passed to Gemini as context so it can handle follow-up questions.
Long-term memory writes the full conversation history to feynman_memory.json on Google Drive. When the notebook starts, load_memory() reads this file back in so the assistant remembers past sessions. You can inspect or reset the memory at any time using show_memory() and reset_conversation().
Gradio Interface
The app is built with Gradio Blocks and has two tabs.
The Chat tab wraps the ask_feynman function in a ChatInterface component. Each response includes the answer from Gemini followed by the source chunk IDs that were retrieved to generate it.
The Memory Dashboard tab shows a Pandas DataFrame of all conversation turns with turn number, user message, and a preview of the assistant response. Clicking the Refresh Memory button reloads the table with the latest turns.

## Known Issues
Retrieval quality depends heavily on how much lecture material is in the corpus. Thin coverage on a topic means weaker answers.
Follow-up questions sometimes retrieve chunks that are relevant to the topic in general but not to the specific follow-up.
The personality is entirely prompt-based, so it captures Feynman's enthusiasm and analogies but is not a precise simulation.
The corpus was assembled manually from lecture text, so there are occasional formatting inconsistencies in how equations and figures are represented.
Gemini 2.5 Flash occasionally returns a 503 when servers are under heavy load. The fix is just to retry the cell.
Long term meory not working the way it should work.

## Future Improvements
Add more lecture chapters, transcripts, and biographical material to improve coverage.
Improve retrieval quality with a reranking step after the initial FAISS search.
Add inline source citations that link directly to specific lecture chapters.
Add speech input and voice output so you can actually have a conversation out loud.
Move the whole thing to a proper web app instead of a Colab notebook.
Improve follow-up handling by injecting the previous question into the retrieval query rather than just the new message.

## Example Questions
What is probability?
Explain entropy.
What is energy?
Why do physicists use models?
Explain uncertainty in simple terms.
How do atoms work?
What is the conservation of energy?
How does quantum mechanics change the way we think about particles?
