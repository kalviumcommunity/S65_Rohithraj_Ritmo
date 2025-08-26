# Ritmo-Playlists

![Node.js](https://img.shields.io/badge/Node.js-16.x-green.svg)
![React](https://img.shields.io/badge/React-17.x-blue.svg)
![MongoDB](https://img.shields.io/badge/MongoDB-6.x-brightgreen.svg)

## Description
Ritmo is an AI-powered tool that curates personalized music playlists based on user preferences (e.g., mood, genre, or occasion like "chill jazz for studying"). Using Retrieval-Augmented Generation (RAG), it retrieves song metadata from a MongoDB database (populated with datasets like Kaggle’s Spotify dataset) and prompts a Large Language Model (LLM) via an API (e.g., OpenAI) to generate tailored playlists with descriptions. Built with the MERN stack (MongoDB, Express.js, React, Node.js), Ritmo supports interactive playlist refinement through a dynamic web interface.

## Features
- **Personalized Playlists**: Generates playlists based on mood, genre, or occasion.
- **RAG Pipeline**: Combines MongoDB-based retrieval with LLM generation for accurate recommendations.
- **Prompting Techniques**: Zero-shot, one-shot, multi-shot, dynamic, and chain-of-thought (CoT) prompting.
- **Evaluation**: Supports test cases to ensure relevance and coherence (test framework setup TBD).
- **Scalability**: Handles large datasets with MongoDB and cloud-ready Express.js backend.

## Usage
1. Start the backend server (`server/`).
2. Launch the React frontend (`client/`).
3. Enter preferences in the web UI (e.g., "workout rock playlist").
4. View JSON playlist (`playlist_name`, `songs`, `description`).
5. Refine by mood, genre, or occasion via the UI.

## Technical Details
- **Stack**: MongoDB (database), Express.js (backend), React (frontend), Node.js (runtime), OpenAI API (LLM), `spotify-web-api-node` (Spotify integration).
- **Dataset**: Kaggle’s Spotify dataset (title, artist, genre, lyrics), stored in MongoDB.
- **Pipeline**:
  1. Load and preprocess song metadata into MongoDB.
  2. Optionally precompute embeddings (e.g., via Python service) and store in MongoDB.
  3. Perform text-based or vector search in MongoDB for song retrieval.
  4. Embed user query, retrieve top-5 songs, and generate JSON playlist via LLM API.
  5. Configure LLM: `temperature=0.8` (creativity), `top_p=0.9` (diversity), `max_tokens=500` (length), `stop=["</playlist>"]` (conciseness), JSON output.
  6. Use Spotify API for additional metadata via function calling.
  7. Evaluate with test cases (e.g., query, expected playlist) for relevance and coherence (TBD).
  8. Optionally compare cosine, L2, or dot product similarities if using vector search (e.g., MongoDB Atlas).

## Setup Instructions
1. **Clone the Repository**:
   ```bash
   git clone <repository-url>
   cd ritmo-playlists
   ```
2. **Backend Setup**:
   - Navigate to `server/`:
     ```bash
     cd server
     npm install
     ```
   - Create `server/.env`:
     ```env
     MONGODB_URI=mongodb://localhost:27017/ritmo
     OPENAI_API_KEY=your-openai-api-key
     SPOTIFY_CLIENT_ID=your-spotify-client-id
     SPOTIFY_CLIENT_SECRET=your-spotify-client-secret
     ```
   - Start MongoDB locally or use MongoDB Atlas.
   - Run the backend:
     ```bash
     npm run dev
     ```
3. **Frontend Setup**:
   - Navigate to `client/`:
     ```bash
     cd client
     npm install
     ```
   - Create `client/.env`:
     ```env
     REACT_APP_API_URL=http://localhost:5000
     ```
   - Start the frontend:
     ```bash
     npm start
     ```
4. **Preprocess Data**:
   - Place the Spotify dataset (e.g., `spotify_dataset.csv`) in `data/raw/`.
   - Run `server/src/data/preprocessData.js` to load data into MongoDB:
     ```bash
     node server/src/data/preprocessData.js data/raw/spotify_dataset.csv data/processed/song_metadata.json
     ```
5. **Access the App**:
   - Open `http://localhost:3000` in your browser.
   - Enter a query (e.g., "chill jazz for studying") to generate a playlist.

## System Prompt
You are Ritmo, an AI playlist curator. Generate personalized playlists based on user preferences (mood, genre, occasion) using retrieved song metadata (title, artist, genre, lyrics). Output a JSON playlist with `playlist_name`, `songs` (5 song objects with `title`, `artist`, `genre`), and `description`. Use `temperature=0.8`, `top_p=0.9`, `max_tokens=500`. Stop at `</playlist>`. Output valid JSON.

## User Prompt Example
Create a JSON playlist for "chill jazz for studying" with 5 songs from provided metadata. Include `playlist_name`, `songs` (title, artist, genre), and `description`.

## RTFC Framework Explanation
- **Role**: System defines Ritmo as an AI playlist curator.
- **Task**: System instructs generating playlists with metadata; user specifies "chill jazz for studying" playlist.
- **Format**: Both require valid JSON with `playlist_name`, `songs`, `description`.
- **Constraints**: 5 songs, creative parameters (`temperature=0.8`, etc.), stop at `</playlist>`, user theme-specific.

## Prompting Techniques
### Zero-Shot Prompting
**What is Zero-Shot Prompting?**  
Zero-shot prompting generates a response based solely on instructions without prior examples or training data. The model relies on its pre-trained knowledge and prompt clarity.

**How It’s Utilized**  
- **System Prompt**: Defines Ritmo’s role and task, instructing playlist generation using metadata without examples. Specifies JSON structure, 5 songs, and LLM parameters (`temperature=0.8`, etc.).
- **User Prompt**: Requests a "chill jazz for studying" playlist, relying on the model’s ability to interpret the theme and metadata.
- The zero-shot approach allows flexible handling of diverse inputs via the RAG pipeline.

### One-Shot Prompting
**What is One-Shot Prompting?**  
One-shot prompting provides a single example to guide the AI’s response, ensuring consistent format and style.

**How It’s Utilized**  
- **System Prompt**: Defines Ritmo’s role and tasks it with generating a JSON playlist, including one example for "upbeat pop for a road trip" to show JSON format and style.
- **User Prompt**: Requests a "relaxing acoustic for a cozy evening" playlist, following the example’s structure.
- The example anchors the response while allowing flexibility.

### Multi-Shot Prompting
**System Prompt**  
You are Ritmo, an AI music playlist curator. Create personalized playlists based on user preferences (e.g., mood, genre, occasion) using song metadata from MongoDB. Output a JSON playlist with `playlist_name`, `songs` (5 song objects with `title`, `artist`, `genre`), and a `description` of the playlist’s vibe. Use multi-shot prompting with provided examples. Ensure variety and query alignment. Set `temperature=0.8`, `top_p=0.9`, `max_tokens=500`. Stop at `</playlist>`. Output valid JSON.

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
Generate a JSON playlist for "energetic hip-hop for a workout" with 5 songs from provided metadata. Include `playlist_name`, `songs` (title, artist, genre), and a `description`, following the examples’ structure and style.

**Explanation**  
Multi-shot prompting uses multiple examples to ensure robust understanding of output format, style, and context. The system prompt provides two examples to guide JSON structure and tone, enhancing coherence for queries like "energetic hip-hop for a workout."

### Dynamic Prompting
**System Prompt**  
You are Ritmo, an AI music playlist curator. Create personalized playlists based on user preferences using song metadata from MongoDB. Use dynamic prompting to adapt based on query complexity. For simple queries (e.g., "happy pop"), generate a straightforward playlist. For detailed queries (e.g., with tempo, era, or artists), incorporate specifics. Output a JSON playlist with `playlist_name`, `songs` (5 song objects with `title`, `artist`, `genre`), and a `description`. Set `temperature=0.8`, `top_p=0.9`, `max_tokens=500`. Stop at `</playlist>`. Output valid JSON.

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
Generate a JSON playlist for "mellow indie folk from the 2010s for a rainy day" with 5 songs from provided metadata. Include `playlist_name`, `songs` (title, artist, genre), and a `description`, adapting to the query’s specific details (genre, era, occasion).

**Explanation**  
Dynamic prompting adjusts the response based on query complexity. Simple queries get basic playlists, while detailed queries (e.g., era-specific) incorporate specifics, using MongoDB for song selection and LLM for generation.

### Chain-of-Thought Prompting
**System Prompt**  
You are Ritmo, an AI music playlist curator. Create personalized playlists based on user preferences using song metadata from MongoDB. Use chain-of-thought prompting: (1) Analyze the query’s mood, genre, and occasion; (2) Select 5 songs matching the criteria, ensuring variety; (3) Craft a playlist name and description. Output a JSON playlist with `playlist_name`, `songs` (5 song objects with `title`, `artist`, `genre`), and a `description`. Set `temperature=0.8`, `top_p=0.9`, `max_tokens=500`. Stop at `</playlist>`. Output valid JSON.

**Example**:
For query "upbeat pop for a road trip":
- Step 1: Query specifies "upbeat pop" (mood: energetic, genre: pop) and "road trip" (occasion: fun, driving).
- Step 2: Select 5 pop songs with high energy, ensuring diverse artists.
- Step 3: Name the playlist "Road Trip Jams" and describe it as energetic.
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
Generate a JSON playlist for "mellow indie folk from the 2010s for a rainy day" with 5 songs from provided metadata. Include `playlist_name`, `songs` (title, artist, genre), and a `description`. Use chain-of-thought prompting to reason through: (1) Analyze the query’s mood, genre, and occasion; (2) Select 5 matching songs with variety; (3) Create a playlist name and description.

**Explanation**  
Chain-of-thought prompting breaks the task into logical steps, improving transparency and accuracy. The system prompt outlines three steps, and the RAG pipeline ensures metadata-driven song selection.


## Evaluation Pipeline

The evaluation pipeline validates the quality of AI-generated playlists by comparing them to expected outputs. It uses a dataset of test cases, an LLM-based judge to score outputs, and a testing framework for automation. The pipeline is implemented as a Node.js script, integrating seamlessly with the MERN stack backend.

### Features
- **Dataset**: Includes 5+ test samples with user queries (e.g., "chill jazz for studying") and expected JSON playlists (with `playlist_name`, `songs`, `description`).
- **Generation**: Simulates the RAG pipeline by generating playlists via the OpenAI API (zero-shot, pending MongoDB integration).
- **Judging**: An LLM judge evaluates outputs on:
  - **Format Adherence**: Valid JSON with required fields (`playlist_name`, `songs` as array of 5 objects each with `title`, `artist`, `genre`, `description`) (5/3/1).
  - **Song Count**: Exactly 5 songs (5/1).
  - **Relevance**: Matches query’s mood, genre, occasion (e.g., "chill" for jazz, "studying" for focus) (1-5).
  - **Coherence**: Description aligns with playlist vibe and is engaging (1-5).
  - **Overall Similarity**: Similarity to expected output in song choices, name, and description (1-5).
- **Testing Framework**: Uses `jest` for automated testing, asserting average scores >= 3.5/5 across criteria. Includes a fallback for manual runs without Jest.
- **Output**: JSON with scores (`format_score`, `song_count_score`, `relevance_score`, `coherence_score`, `overall_similarity_score`) and `comments` for each test case.

### Setup Instructions
1. **Install Dependencies**:
   ```bash
   cd server
   npm install openai jest --save-dev
   ```
2. **Configure Environment**:
   - Update `server/.env` with:
     ```env
     OPENAI_API_KEY=your-openai-api-key
     ```
3. **Add Evaluation Script**:
   - Place `evaluationPipeline.js` in `server/src/evaluation/`.
   - Example script structure:
     ```javascript
     const { OpenAI } = require('openai');
     const openai = new OpenAI({ apiKey: process.env.OPENAI_API_KEY });

     const DATASET = [
       {
         query: 'chill jazz for studying',
         expected: {
           playlist_name: 'Study Jazz Vibes',
           songs: [
             { title: 'Blue in Green', artist: 'Miles Davis', genre: 'Jazz' },
             // ... 4 more songs
           ],
           description: 'A soothing jazz playlist to enhance focus during your study sessions.'
         }
       },
       // ... 4 more samples
     ];

     async function generatePlaylist(query) { /* Calls OpenAI API */ }
     async function judgeOutput(query, expected, generated) { /* Evaluates output */ }
     ```
4. **Add Test File**:
   - Create `server/src/evaluation/evaluationPipeline.test.js` for Jest tests.
5. **Run the Pipeline**:
   - Run tests:
     ```bash
     cd server
     npm run test
     ```
   - For manual runs:
     ```bash
     node server/src/evaluation/evaluationPipeline.js
     ```
   - Add to `server/package.json`:
     ```json
     "scripts": {
       "test": "jest",
       "eval": "node src/evaluation/evaluationPipeline.js"
     }
     ```

### Technical Details
- **Files**: `server/src/evaluation/evaluationPipeline.js` (core logic) and `evaluationPipeline.test.js` (tests).
- **Dataset**: 5 samples covering diverse queries (e.g., "upbeat pop for a road trip", "mellow indie folk from the 2010s for a rainy day"). Expected outputs use real songs sourced from platforms like Spotify and Reddit.
- **Generation**: Uses OpenAI’s `gpt-4o-mini` with `temperature=0.8`, `top_p=0.9`, `max_tokens=500`. Future integration with MongoDB for RAG-based retrieval using `spotify-web-api-node`.
- **Judging**: Separate LLM call with `temperature=0.2` for consistent scoring, evaluating 5 criteria via a structured prompt.
- **Testing**: Jest runs tests asynchronously, with a 30s timeout for API calls. Manual loop for non-Jest execution.
- **Error Handling**: Catches invalid JSON and provides detailed `comments` for debugging (e.g., "Generated playlist matches genre but has minor song differences").

### Example Output
For query "chill jazz for studying":
```json
{
  "query": "chill jazz for studying",
  "generated": {
    "playlist_name": "Study Jazz Vibes",
    "songs": [
      {"title": "Blue in Green", "artist": "Miles Davis", "genre": "Jazz"},
      // ... 4 more songs
    ],
    "description": "A soothing jazz playlist for focused studying."
  },
  "scores": {
    "format_score": 5,
    "song_count_score": 5,
    "relevance_score": 4,
    "coherence_score": 4,
    "overall_similarity_score": 4,
    "comments": "Generated playlist matches expected genre and mood, with minor song differences."
  },
  "average": 4.4
}
```

### Notes
- **Integration**: Expose as an Express route (e.g., `/api/evaluate`) or run standalone. Use `child_process` for scripted execution if needed.
- **Future Work**: Integrate MongoDB for RAG-based generation, expand dataset, or explore open-source LLMs (e.g., Hugging Face models).
- **Contributing**: Add test cases to `DATASET` in `evaluationPipeline.js` or refine judge criteria. Submit PRs with test results.

## Contributing
- Fork the repository.
- Create feature branches (e.g., `feature/zero-shot`, `feature/rag-pipeline`).
- Submit pull requests with tests and documentation.
- Validate features using test cases (TBD).

## Notes
- **Vector Search**: Currently uses MongoDB text search; consider MongoDB Atlas vector search or external databases like Pinecone for enhanced performance.
- **Embeddings**: Precompute embeddings via a Python service and store in MongoDB for simplicity.
- **LLM**: Uses Gemini API; explore Hugging Face for open-source alternatives.