# CivicAssist Local Government Scheme Assistant

CivicAssist is a local-first Streamlit prototype that helps citizens find relevant government schemes using a retrieval-augmented generation (RAG) flow, local FAISS vector search, and free open-source Ollama models.

It is designed to run without paid APIs, without API keys by default, and without cloud services. The app supports:

- SLM mode
- LLM mode
- Retrieval Only mode
- Local FAISS vector storage
- Local Ollama model detection and status checks
- Local model comparison

## Free and open-source architecture

- Streamlit
- Python
- SentenceTransformers
- FAISS
- Ollama
- Local government scheme dataset

## Model architecture

Citizen -> Streamlit UI -> Profile Extraction -> Query Generation -> Embedding Generation -> FAISS Vector DB -> Relevant scheme documents -> Eligibility Engine -> SLM or LLM mode -> Output validation -> CivicAssist response

## Local model requirements

This app is designed to run with locally installed Ollama models. The base URL is configurable through the environment variable:

```bash
OLLAMA_BASE_URL=http://localhost:11434
```

The default model configuration is configurable in code or through the environment.

Example configuration in `.env` or `.env.example`:

```bash
OLLAMA_BASE_URL=http://localhost:11434
EMBEDDING_MODEL=sentence-transformers/all-MiniLM-L6-v2
MODEL_CONFIG={"slm_models":[{"name":"phi3","category":"SLM"},{"name":"gemma","category":"SLM"}],"llm_models":[{"name":"llama3.2","category":"LLM"}]}
```

The prototype never requires OpenAI, Gemini, or any paid cloud LLM key.

## Recommended model configuration

Suggested small-model choices:

- Phi-3 mini
- Gemma small variant

Suggested larger local model choices:

- Llama 3.2
- Qwen 2.5
- Gemma 7B/8B variants

The app will use the best configured model that is available locally.

## Hardware-aware defaults

### Low-spec system

- Use only a small model such as Phi-3
- Retrieval Only mode is the most reliable fallback

### Medium-spec system

- Use Phi-3 plus a local moderate model such as Llama 3.2 3B/8B

### High-spec system

- Compare a small model and a larger 7B/8B/14B local model
- Run full LLM mode for richer answer generation

Approximate requirements:

- 4 GB RAM minimum for retrieval-only mode
- 8 GB+ RAM recommended for embedding and light model usage
- 16 GB+ RAM recommended for larger local models
- GPU optional; CPU-based local inference is supported

## Setup

### 1. Install Ollama

Follow the official Ollama install guide for your platform.

### 2. Pull a local model

```bash
ollama pull phi3
ollama pull gemma
ollama pull llama3.2
```

The app will automatically detect whether a required model is installed. If it is missing, the UI will display instructions such as:

```text
Model not found.
Please install the selected model using:
ollama pull phi3
```

### 3. Create a virtual environment and install dependencies

On Windows, use the Python 3.12 runtime explicitly when the default `python` command points to another installation:

```powershell
py -3.12 -m venv .venv
.venv\Scripts\activate
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

### 4. Run the application

```powershell
python -m streamlit run app.py
```

If you are using a Windows machine where the system `python` command is not the correct interpreter, run:

```powershell
py -3.12 -m streamlit run app.py
```

## Important fallback mode

The app supports three modes:

1. SLM mode
2. LLM mode
3. Retrieval Only mode

Retrieval Only mode:

- retrieves relevant government schemes
- shows the original scheme information
- shows eligibility conditions
- shows source URLs
- avoids generative response when a model is unavailable

## Embedding model

The default embedding model is:

```text
sentence-transformers/all-MiniLM-L6-v2
```

This downloads automatically when first used.

## Vector database

The application stores local vector index files under:

```text
vector_db/
```

The data is stored locally using FAISS.

## Model comparison

The app can run a local comparison using multiple models and save CSV output to:

```text
results/model_comparison.csv
```

Each row records:

- model name
- model category
- parameter size
- average latency
- response length
- retrieval relevance
- groundedness score
- response quality
- deployment feasibility
- hardware requirement

## Data sources

The app uses local dataset entries derived from official government sources and includes source URLs in the UI. These are reference links and should be verified in the final decision-making process.

## Running on Windows

This project includes explicit support for Windows Python environments where the system `python` command may resolve to the wrong interpreter.

### Step 1: Install Ollama

1. Download Ollama from [ollama.ai](https://ollama.ai)
2. Install and run the Ollama server
3. Verify it's running: `curl http://localhost:11434/api/tags`

### Step 2: Pull a Local Model

Open a terminal and run:

```powershell
ollama pull phi3
ollama pull gemma
ollama pull llama3.2
```

These are free open-weight models that run locally.

### Step 3: Clone and Setup the Project

```powershell
cd e:\llm-project
py -3.12 -m venv .venv
.venv\Scripts\activate
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

### Step 4: Configure Environment (Optional)

The `.env` file is pre-configured with defaults. To customize:

```powershell
# Edit .env with your preferred editor
# Example content (already set by default):
# OLLAMA_BASE_URL=http://localhost:11434
# EMBEDDING_MODEL=sentence-transformers/all-MiniLM-L6-v2
# MODEL_CONFIG={"slm_models":[{"name":"phi3","category":"SLM"},{"name":"gemma","category":"SLM"}],"llm_models":[{"name":"llama3.2","category":"LLM"}]}
```

### Step 5: Run the Application

```powershell
# Activate venv if not already active
.venv\Scripts\activate

# Run Streamlit
python -m streamlit run app.py
```

This opens the app at `http://localhost:8501`

### Troubleshooting on Windows

**Issue**: Command not found or wrong Python version

**Solution**: Always use `py -3.12` explicitly:
```powershell
py -3.12 -m streamlit run app.py
```

**Issue**: Model not found error

**Solution**: Ensure Ollama is running and the model is installed:
```powershell
# Terminal 1: Start Ollama (if not already running)
ollama serve

# Terminal 2: Install missing model
ollama pull phi3
```

**Issue**: "Ollama is not running"

**Solution**: The app needs Ollama to be actively running. Start it in a separate terminal:
```powershell
ollama serve
```

### Working Without GPU

The app is optimized to work on CPU-only systems:

- Small models (Phi-3, Gemma) run efficiently on 4GB+ RAM
- FAISS vector search is CPU-based
- Sentence-Transformers uses CPU by default
- No GPU required for retrieval mode

## Notes

- This project does not require OpenAI, Gemini, or any paid cloud LLM.
- It can run using local free models through Ollama.
- The app will gracefully fall back when larger models are unavailable.
- All data is stored locally in `vector_db/` using FAISS.
- Model comparison results are saved to `results/model_comparison.csv`.
