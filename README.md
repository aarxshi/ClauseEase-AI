# ClauseEase AI

This project implements a local, privacy-preserving document assistant capable of processing PDF and text files, extracting and chunking content, detecting language, and performing summarization, translation, and document-based question answering. The system uses a custom Streamlit UI and the LLaMA 3 model served locally through Ollama. All data remains on the user's machine, enabling secure analysis of private or sensitive documents.

The assistant supports:

- High-speed PDF extraction with PyMuPDF  
- Automatic language detection using `langdetect`  
- Chunking of long documents for effective context handling  
- Real-time token streaming from a locally hosted LLM  
- Document-grounded summarization, translation, and Q&A  


## 1. Overview

The motivation behind this project is to provide an offline alternative to cloud-based LLM tools for reading and interpreting documents. By running **LLaMA 3** through **Ollama**, the application avoids external API calls entirely.

The Streamlit interface supports multi-file uploads, instant previews, file-specific actions, and an interactive chat that streams responses incrementally, closely mirroring modern LLM interfaces.


## 2. File Processing Pipeline

### PDF Processing
- Files are identified using header inspection (`%PDF`) to avoid extension-based misclassification.  
- Text extraction is performed using **PyMuPDF (fitz)**, which provides significantly faster and cleaner results than standard PDF libraries.  
- A short preview (first ~500 characters) is shown immediately in the chat interface before full extraction completes.  

### TXT Processing
- Text files are decoded using UTF-8, with a fallback to Latin-1 for broader compatibility.  
- Extraction is nearly instantaneous due to the absence of complex structure.  

### Language Detection
- Detected from the first 500 characters using `langdetect`, enabling translation and customized responses.


## 3. Chunking Strategy

Large documents exceed the context window of most local LLMs.  
To handle this, the project uses a simple and efficient chunking method:

- Split text into words  
- Aggregate until reaching ~800 characters per chunk  
- Store all resulting segments in order  
- Use these chunks for summarization and document-specific Q&A  

This approach avoids memory overload and maintains coherence without requiring vector databases or embeddings.


## 4. Model and Local Inference

The assistant uses:

- **Model:** `llama3:latest`  
- **Runtime:** Ollama's local inference server  
- **Endpoint:** `http://localhost:11434/api/generate`  

Responses are streamed token-by-token using `iter_lines()`, allowing the UI to update incrementally. The interface displays partial output with a cursor indicator and includes a stop button to interrupt generation.


## 5. Streamlit Interface

The UI includes:

- Gradient-styled layout with custom CSS  
- Chat bubbles for user and assistant messages  
- Multi-file upload panel  
- Real-time streaming display  
- Automatic chat history management  
- Per-file storage of full text, chunks, language metadata, and original content  

Uploaded files appear in the chat with a preview and confirmation message once fully processed.


## 6. Summarization, Translation, and Q&A

The system supports three file-specific operations:

### Summarization
Extracts up to 4000 characters and requests a structured summary.

### Translation
Uses the detected language and translates the original text into natural English.

### Document-grounded Q&A
Answers questions using only the text from the selected document.  
The prompt enforces grounding to reduce hallucination.

If no file-specific intent is detected, the model responds in general chat mode.


## 7. Folder Structure
```
document-assistant/
│
├── app.py # main streamlit application
├── requirements.txt # dependencies
│
├── screenshots/ # UI images for README
│
└── README.md
```
## 8. Running the Project

### 1. Install Ollama
Download from:  
https://ollama.com

### 2. Pull the model
```bash
ollama pull llama3:latest
```

### 3. Install Python dependencies
```bash
pip install -r requirements.txt
```

### 4. Start the application
```bash
streamlit run app.py
```
