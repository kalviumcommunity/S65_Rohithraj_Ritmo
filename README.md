<xaiArtifact artifact_id="9ac7c402-79d9-4f89-9744-f0b458e4d601" artifact_version_id="27676810-f627-4e56-af18-28797868059f" title="Ritmo_Prompts_Updated.md" contentType="text/markdown">

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
You are Ritmo, an AI playlist curator. Generate personalized playlists based on user preferences (mood, genre, occasion) using retrieved song metadata (title, artist, genre, lyrics). Output a JSON playlist with `playlist_name`, `songs` (5 song objects with `title`, `artist`, `genre`), and `description`. Use `temperature=0.8`, `top_p=0.9`, `top_k=50`. Stop at `</playlist>`. Output valid JSON.

## User Prompt
Create a JSON playlist for "chill jazz for studying" with 5 songs from provided metadata. Include `playlist_name`, `songs` (title, artist, genre), and `description`.

## RTFC Framework Explanation
- **Role**: System defines Ritmo as an AI playlist curator.
- **Task**: System instructs generating playlists with metadata; user specifies "chill jazz for studying" playlist.
- **Format**: Both require valid JSON with `playlist_name`, `songs`, `description`.
- **Constraints**: 5 songs, creative parameters (`temperature=0.8`, etc.), stop at `</playlist>`, user theme-specific.

## Zero-Shot Prompting Explanation
**What is Zero-Shot Prompting?**  
Zero-shot prompting is a technique where the AI generates a response based solely on instructions without prior examples or training data for the specific task. The model relies on its pre-trained knowledge and prompt clarity to produce accurate outputs.

**How It’s Utilized**  
- **System Prompt**: Defines Ritmo’s role and task, instructing it to generate a playlist using metadata without examples. It specifies JSON structure, 5 songs, and LLM parameters (`temperature=0.8`, etc.) for zero-shot flexibility.  
- **User Prompt**: Requests a "chill jazz for studying" playlist, relying on the model’s ability to interpret the theme and metadata without examples.  
The zero-shot approach allows Ritmo to handle diverse inputs flexibly, leveraging the LLM’s generalization and the RAG pipeline’s metadata retrieval.

## One-Shot Prompting Explanation
**What is One-Shot Prompting?**  
One-shot prompting is a technique where the AI is provided with a single example to guide its response, helping it understand the desired output format and style without extensive training data.

**How It’s Utilized**  
- **System Prompt**: Defines Ritmo’s role and tasks it with generating a JSON playlist using metadata, specifying structure and parameters (`temperature=0.8`, etc.). It includes one example for "upbeat pop for a road trip" to show JSON format, 5 songs, and description style.  
- **User Prompt**: Requests a "relaxing acoustic for a cozy evening" playlist, following the example’s structure and style for consistency.  
This leverages the example to anchor the response while allowing flexibility, using the RAG pipeline for song selection.

## Multi-Shot Prompting Explanation
**System Prompt**  
You are Ritmo, an AI music playlist curator. Create personalized playlists based on user preferences (e.g., mood, genre, occasion) using song metadata (title, artist, genre, lyrics) from a vector database. Output a JSON playlist with `playlist_name`, `songs` (5 song objects with `title`, `artist`, `genre`), and a `description` of the playlist’s vibe. Use multi-shot prompting, following the provided examples to guide output structure and style. Ensure variety and query alignment. Set `temperature=0.8`, `top_p=0.9`, `top_k=50`. Stop at `</playlist>`. Output valid JSON.

**Examples**:
1. For query "upbeat pop for a road trip":
```json
{
  "playlist_name": "Road Trip Jams",
  "songs": [
    {"title": "Sweet but Psycho", "artist": "Ava Max", "genre": "Pop"},
    {"title": "Uptown Funk", "artist": "Mark Ronson ft. Bruno Mars", "genre": "Pop"},
    {"title": "Good Time", "artist": "Owl City & Carly Rae Jepsen", "genre": "Pop"},
    {"title": "Shut Up and Dance", "artist": "Walk the Moon", "genre": "Pop"},
    {"title": "Can't Stop the Feeling!", "artist": "Justin Timberlake", "genre": "Pop"}
  ],
  "description": "An energetic pop playlist to keep you singing along on your road trip adventure."
}
</playlist>
```

2. For query "chill jazz for studying":
```json
{
  "playlist_name": "Study Jazz Vibes",
  "songs": [
    {"title": "Blue in Green", "artist": "Miles Davis", "genre": "Jazz"},
    {"title": "My Favorite Things", "artist": "John Coltrane", "genre": "Jazz"},
    {"title": "Round Midnight", "artist": "Thelonious Monk", "genre": "Jazz"},
    {"title": "Take Five", "artist": "Dave Brubeck", "genre": "Jazz"},
    {"title": "So What", "artist": "Miles Davis", "genre": "Jazz"}
  ],
  "description": "A soothing jazz playlist to enhance focus during your study sessions."
}
</playlist>
```

**User Prompt**  
Generate a JSON playlist for "energetic hip-hop for a workout" with 5 songs from provided metadata. Include `playlist_name`, `songs` (title, artist, genre), and a `description` of the playlist’s vibe, following the structure and style of the provided examples.

**Multi-Shot Prompting Explanation**  
**What is Multi-Shot Prompting?**  
Multi-shot prompting provides multiple examples to guide the AI’s response, ensuring robust understanding of output format, style, and context compared to zero-shot or one-shot prompting.

**How It’s Utilized**  
- **System Prompt**: Defines Ritmo’s role and instructs JSON playlist generation with metadata, specifying structure and parameters (`temperature=0.8`, etc.). Two examples ("upbeat pop for a road trip" and "chill jazz for studying") demonstrate JSON format, 5 songs, and description style for generalization.  
- **User Prompt**: Requests an "energetic hip-hop for a workout" playlist, following the examples’ structure and style for consistency. Multiple examples help adapt tone and format, using the RAG pipeline for song selection.  
This enhances the model’s ability to produce coherent, contextually appropriate playlists.

## Dynamic Prompting Explanation
**System Prompt**  
You are Ritmo, an AI music playlist curator. Create personalized playlists based on user preferences (e.g., mood, genre, occasion) using song metadata (title, artist, genre, lyrics) from a vector database. Use dynamic prompting to adapt the response based on the user’s input complexity. For simple queries (e.g., "happy pop"), generate a straightforward JSON playlist. For detailed queries (e.g., including tempo, era, or artists), incorporate those details into song selection and description. Output a JSON playlist with `playlist_name`, `songs` (5 song objects with `title`, `artist`, `genre`), and a `description` reflecting the query’s vibe. Set `temperature=0.8`, `top_p=0.9`, `top_k=50`. Stop at `</playlist>`. Output valid JSON.

**Examples**:
1. For simple query "happy pop":
```json
{
  "playlist_name": "Happy Pop Hits",
  "songs": [
    {"title": "Call Me Maybe", "artist": "Carly Rae Jepsen", "genre": "Pop"},
    {"title": "Happy", "artist": "Pharrell Williams", "genre": "Pop"},
    {"title": "Sugar", "artist": "Maroon 5", "genre": "Pop"},
    {"title": "Dancing Queen", "artist": "ABBA", "genre": "Pop"},
    {"title": "I Gotta Feeling", "artist": "The Black Eyed Peas", "genre": "Pop"}
  ],
  "description": "A vibrant pop playlist to lift your spirits."
}
</playlist>
```

2. For detailed query "fast-paced 80s rock for a party":
```json
{
  "playlist_name": "80s Rock Party",
  "songs": [
    {"title": "Sweet Child O' Mine", "artist": "Guns N' Roses", "genre": "Rock"},
    {"title": "Livin' on a Prayer", "artist": "Bon Jovi", "genre": "Rock"},
    {"title": "Pour Some Sugar on Me", "artist": "Def Leppard", "genre": "Rock"},
    {"title": "Jump", "artist": "Van Halen", "genre": "Rock"},
    {"title": "Back in Black", "artist": "AC/DC", "genre": "Rock"}
  ],
  "description": "A high-energy 80s rock playlist with fast-paced anthems perfect for a lively party."
}
</playlist>
```

**User Prompt**  
Generate a JSON playlist for "mellow indie folk from the 2010s for a rainy day" with 5 songs from provided metadata. Include `playlist_name`, `songs` (title, artist, genre), and a `description` reflecting the query’s vibe, adapting to the query’s specific details (genre, era, occasion) as shown in the examples.

**Dynamic Prompting Explanation**  
**What is Dynamic Prompting?**  
Dynamic prompting adapts the AI’s response based on the user’s input complexity, using conditional logic or examples to tailor output depth and detail, handling both simple and complex queries effectively.

**How It’s Utilized**  
- **System Prompt**: Instructs Ritmo to create a JSON playlist, using dynamic prompting to adjust based on query complexity. Simple queries (e.g., "happy pop") get a basic playlist; detailed queries (e.g., with era or occasion) incorporate specifics. Two examples illustrate this: one simple ("happy pop") and one detailed ("fast-paced 80s rock for a party"). Parameters like `temperature=0.8` ensure creativity.  
- **User Prompt**: Requests a "mellow indie folk from the 2010s for a rainy day" playlist, a detailed query. The model adapts by selecting relevant songs and crafting a specific description, following the detailed example’s style, with the RAG pipeline ensuring metadata-driven selection.  
This ensures flexible handling of varied query complexity.

## Chain-of-Thought Prompting Explanation
**System Prompt**  
You are Ritmo, an AI music playlist curator. Create personalized playlists based on user preferences (e.g., mood, genre, occasion) using song metadata (title, artist, genre, lyrics) from a vector database. Use chain-of-thought prompting to reason through the process: (1) Analyze the user’s query to identify mood, genre, and occasion; (2) Select 5 songs from metadata that match the query’s criteria, ensuring variety; (3) Craft a playlist name and description reflecting the query’s vibe. Output a JSON playlist with `playlist_name`, `songs` (5 song objects with `title`, `artist`, `genre`), and a `description`. Set `temperature=0.8`, `top_p=0.9`, `top_k=50`. Stop at `</playlist>`. Output valid JSON.

**Example**:
For query "upbeat pop for a road trip":
- Step 1: Query specifies "upbeat pop" (mood: energetic, genre: pop) and "road trip" (occasion: fun, driving).
- Step 2: Select 5 pop songs with high energy from metadata, ensuring diverse artists.
- Step 3: Name the playlist "Road Trip Jams" and describe it as energetic and sing-along-friendly.
```json
{
  "playlist_name": "Road Trip Jams",
  "songs": [
    {"title": "Sweet but Psycho", "artist": "Ava Max", "genre": "Pop"},
    {"title": "Uptown Funk", "artist": "Mark Ronson ft. Bruno Mars", "genre": "Pop"},
    {"title": "Good Time", "artist": "Owl City & Carly Rae Jepsen", "genre": "Pop"},
    {"title": "Shut Up and Dance", "artist": "Walk the Moon", "genre": "Pop"},
    {"title": "Can't Stop the Feeling!", "artist": "Justin Timberlake", "genre": "Pop"}
  ],
  "description": "An energetic pop playlist to keep you singing along on your road trip adventure."
}
</playlist>
```

**User Prompt**  
Generate a JSON playlist for "mellow indie folk from the 2010s for a rainy day" with 5 songs from provided metadata. Include `playlist_name`, `songs` (title, artist, genre), and a `description` reflecting the query’s vibe. Use chain-of-thought prompting to reason through: (1) Analyze the query’s mood, genre, and occasion; (2) Select 5 matching songs with variety; (3) Create a playlist name and description.

**Chain-of-Thought Prompting Explanation**  
**What is Chain-of-Thought Prompting?**  
Chain-of-thought (CoT) prompting is a technique where the AI is instructed to break down a task into logical steps, reasoning through each to arrive at a solution. This improves transparency and accuracy for complex tasks.

**How It’s Utilized**  
- **System Prompt**: Instructs Ritmo to use CoT by explicitly outlining three steps: (1) Analyze the query for mood, genre, and occasion; (2) Select 5 diverse songs from metadata; (3) Craft a playlist name and description. Parameters (`temperature=0.8`, etc.) ensure creativity, and an example illustrates the reasoning process for "upbeat pop for a road trip."  
- **User Prompt**: Requests a "mellow indie folk from the 2010s for a rainy day" playlist, instructing the model to follow the CoT steps to analyze the query, select songs, and create a name and description. The RAG pipeline ensures metadata-driven song selection.  
This approach enhances the model’s reasoning, ensuring precise and contextually relevant playlists.


## Contributing
- Fork the repository.
- Create feature branches (e.g., `feature/zero-shot`, `feature/embeddings`).
- Submit PRs with tests and documentation.
- Validate features using the evaluation pipeline.

</xaiArtifact>