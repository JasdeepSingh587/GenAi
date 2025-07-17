What is RAG?
RAG stands for Retrieval-Augmented Generation. It's an AI technique that combines:

Information retrieval (getting relevant data/documents)

Text generation (using a language model like GPT)

So instead of answering from just the model’s memory, the system first searches your custom documents and then generates an answer based on them.

<img width="750" height="355" alt="Rag Model" src="https://github.com/user-attachments/assets/c56fd316-08f8-4920-89d2-a68eaf378fb0" />



What is Vector Database?
A vector database is a type of database designed specifically to store, index, and search high-dimensional vectors efficiently. These vectors usually represent complex data like text, images, audio, or video after being processed by machine learning models (e.g., transformers or embedding models).

In traditional databases, you search using exact values (e.g., names, IDs). In contrast, a vector database allows "semantic" or "similarity" search — it finds data that's similar in meaning, not just exact matches.

Example:

You search for: "best restaurants for breakfast"

How It Works:
Embedding:

Data (text, image, etc.) is converted into a vector (array of numbers) using an embedding model like OpenAI, BERT, etc.

Example: "AI is powerful" → [0.21, -0.34, ..., 0.98]

Storage:

These vectors are stored in a vector database.

Similarity Search:

When a user query is embedded into a vector, the database finds the nearest vectors (most similar ones) using algorithms like cosine similarity or Euclidean distance.

example of vector db is faiss

The database returns entries like "top brunch spots" or "morning cafes near me" — because they're semantically similar.
