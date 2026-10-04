# ShelfSight — Retail Shelf Intelligence

ShelfSight is a portfolio-ready presentation of the original Planogram Vision Agent workflow. It retains the original computer-vision and cloud-AI architecture rather than substituting a local OpenCV heuristic detector.

## workflow 

1. **Shelf-row detection with Roboflow** — the configured row model detects shelf rows/polygons.
2. **Row cropping and product detection** — the original `detect_crop.py` pipeline crops shelf rows and runs the configured Roboflow product model, then annotates results.
3. **Azure Blob Storage pipeline** — uploaded images and generated outputs are stored/retrieved through Azure Blob Storage; processed outputs can be reused.
4. **Agentic analysis** — `llm_processing.py` orchestrates the existing AutoGen + Azure OpenAI reasoning flow over row images.
5. **Semantic image search** — `semantic.py` indexes Azure Blob images with Azure AI Vision embeddings and Azure AI Search vector search. The UI also uses Azure OpenAI to validate retrieved candidates.
6. **Streamlit UI** — `main.py` provides the upload/select, processing, gallery, and semantic search experience.

## Requirements

- Python 3.10 recommended for compatibility with the original AutoGen 0.2 API.
- A Roboflow API key and access to the configured row and product models.
- Azure Blob Storage, Azure AI Vision, Azure AI Search, and Azure OpenAI resources for the cloud-backed features.

## Setup (Windows PowerShell)

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
Copy-Item .env.example .env
```

Edit `.env` and fill in your own credentials. Keep the secret values private. If your Azure Blob Storage connection string is the same for both settings, set it in both `AZURE_BLOB_CONN` and `AZURE_STORAGE_CONNECTION_STRING`.

Start the original Streamlit application:

```powershell
python -m streamlit run main.py
```

Then open `http://localhost:8501`.

macOS/Linux users can use `python -m venv .venv`, `source .venv/bin/activate`, `cp .env.example .env`, and then the same `pip install` and Streamlit commands.

## Configuration notes

- `ROBOFLOW_API_KEY`: a valid key for your Roboflow account.
- `ROBOFLOW_WORKSPACE`, `ROBOFLOW_ROW_PROJECT`, `ROBOFLOW_ROW_VERSION`: row-detection model configuration.
- `ROBOFLOW_PRODUCT_PROJECT`, `ROBOFLOW_PRODUCT_VERSION`: product-detection model configuration.
- `AZURE_BLOB_CONN`, `AZURE_BLOB_CONTAINER`: storage used by the Streamlit app.
- `AZURE_STORAGE_CONNECTION_STRING`, `AZURE_STORAGE_CONTAINER`: storage configuration read by the semantic indexer.
- `AZURE_SEARCH_ENDPOINT1`, `AZURE_SEARCH_ADMIN_KEY1`, `AZURE_SEARCH_INDEX_NAME2`: Azure AI Search vector index.
- `AZURE_AI_VISION_API_KEY`, `AZURE_AI_VISION_REGION`, `AZURE_AI_VISION_ENDPOINT`: embeddings used for text-to-image retrieval.
- `AZURE_OPENAI_ENDPOINT`, `AZURE_OPENAI_API_KEY`, `AZURE_OPENAI_API_VERSION`, `AZURE_OPENAI_DEPLOYMENT_NAME`: LLM analysis. `AZURE_OPENAI_DEPLOYMENT` is also used by the UI's GPT validation client.

## Project structure

```text
ShelfSight/
├── main.py                 # Original Streamlit interface
├── detect_crop.py          # Roboflow row/product detection pipeline
├── blob_pipeline_trigger.py# Persist/reuse processed images in Azure Blob
├── llm_processing.py       # AutoGen + Azure OpenAI orchestration
├── semantic.py             # Azure Vision embeddings + Azure AI Search
├── .env.example            # Safe configuration template
├── requirements.txt
└── .streamlit/config.toml
```
