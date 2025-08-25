# Ritmo-Playlists

![Python](https://img.shields.io/badge/python-3.8+-blue.svg)

## Description
Ritmo is an AI-powered tool that curates personalized music playlists based on user preferences (e.g., mood, genre, or occasion like "chill jazz for studying"). Using Retrieval-Augmented Generation (RAG), it retrieves song metadata from datasets like Kaggle’s Spotify dataset and prompts a Large Language Model (LLM) to generate tailored playlists with descriptions. Built with Python, LangChain, Sentence-Transformers, Chroma, and Streamlit, Ritmo supports interactive playlist refinement.

## Features
- **Personalized Playlists**: Generates playlists based on mood, genre, or occasion.
- **RAG Pipeline**: Combines vector-based retrieval with LLM generation for accurate recommendations.
- **Prompting Techniques**: Zero-shot, one-shot, multi-shot, dynamic, and chain-of-thought (CoT) prompting.
- **Evaluation**: Automated pipeline with 5+ test cases to ensure relevance and coherence.
- **Scalability**: Handles large datasets and traffic with Chroma and cloud-ready design.

## Usage
1. Launch the Streamlit UI.
2. Enter preferences (e.g., "workout rock playlist").
3. View JSON playlist (`playlist_name`, `songs`, `description`).
4. Refine by mood, genre, or occasion.

## Technical Details
- **Stack**: Python, LangChain (RAG), OpenAI API (LLM), Sentence-Transformers (`all-MiniLM-L6-v2`), Chroma (vector DB), Streamlit (UI).
- **Dataset**: Kaggle’s Spotify dataset (title, artist, genre, lyrics).
- **Pipeline**:
  1. Load and preprocess song metadata into text chunks.
  2. Generate embeddings with Sentence-Transformers.
  3. Index embeddings in Chroma for fast similarity search.
  4. Embed user query, retrieve top-5 songs, generate JSON playlist via LLM.
  5. Configure LLM: `temperature=0.8` (creativity), `top_p=0.9` (diversity), `top_k=50` (coherence), `stop=["</playlist>"]` (conciseness), JSON output.
  6. Use Spotify API for additional metadata via function calling.
  7. Evaluate with 5+ test cases (query, expected playlist); score relevance and coherence.
  8. Compare cosine, L2, and dot product similarities for retrieval accuracy via PRs.

## System Prompt
You are Ritmo, an AI playlist curator. Generate personalized playlists based on user preferences (mood, genre, occasion) using retrieved song metadata (title, artist, genre, lyrics). Output a JSON playlist with playlist_name, songs (5 song objects with title, artist, genre), and description. Use temperature=0.8, top_p=0.9, top_k=50. Stop at </playlist>. Output valid JSON.

## User Prompt
Create a JSON playlist for "chill jazz for studying" with 5 songs from provided metadata. Include playlist_name, songs (title, artist, genre), and description.

## RTFC Framework Explanation

Role: System defines AI as Ritmo, a playlist curator.
Task: System instructs generating playlists with metadata; user specifies "chill jazz for studying" playlist.
Format: Both require valid JSON with playlist_name, songs, description.
Constraints: 5 songs, creative parameters (temperature=0.8, etc.), stop at </playlist>, user theme-specific.

## Zero-Shot Prompting Explanation

What is Zero-Shot Prompting?Zero-shot prompting is a technique where the AI generates a response based solely on instructions without being provided prior examples or training data for the specific task. The model relies on its pre-trained knowledge and the clarity of the prompt to produce accurate outputs.

How Zero-Shot Prompting is Utilized

System Prompt: Defines Ritmo’s role and task clearly, instructing it to generate a playlist using metadata without providing example outputs. It specifies the JSON structure, song count (5), and LLM parameters (temperature=0.8, etc.) to guide the model in a zero-shot context.

User Prompt: Requests a specific playlist ("upbeat pop for a road trip") without example playlists, relying on the model’s ability to interpret the theme and metadata. The prompt’s clarity ensures the model can generate a relevant playlist from scratch.

The zero-shot approach allows Ritmo to handle diverse user inputs (e.g., any mood or genre) flexibly, leveraging the LLM’s generalization capabilities and the RAG pipeline’s metadata retrieval for accuracy.

## One-Shot Prompting Explanation

What is One-Shot Prompting?
One-shot prompting is a technique where the AI is provided with a single example to guide its response, helping it understand the desired output format and style without extensive training data.

How It’s Utilized

System Prompt: Defines Ritmo’s role and tasks it with generating a JSON playlist using metadata, specifying the structure and parameters (temperature=0.8, etc.). It includes a single example for "upbeat pop for a road trip" to demonstrate the expected JSON format, song count (5), and description style, enabling one-shot learning.

User Prompt: Requests a "relaxing acoustic for a cozy evening" playlist, instructing the model to follow the example’s structure and style, ensuring consistency in output.
This approach leverages the example to anchor the model’s response while allowing flexibility for the new query, using the RAG pipeline for relevant song selection.

## Contributing
- Fork the repository.
- Create feature branches (e.g., `feature/zero-shot`, `feature/embeddings`).
- Submit PRs with tests and documentation.
- Validate features using the evaluation pipeline.