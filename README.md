<p align="center">
  <img src="logo.png" alt="Logo" width="200">
</p>



# Unsiloed AI

Unsiloed AI simplifies document processing, parsing diverse formats — including advanced PDF understanding — and providing seamless integrations with the GenAI ecosystem.
Perfect for chunking, structured extraction, and powering high-quality RAG pipelines.

##  Features

### Document Chunking
- **Supported File Types**: PDF, DOCX, PPTX
- **Chunking Strategies**:
  - **Fixed Size**: Splits text into chunks of specified size with optional overlap
  - **Page-based**: Splits PDF by pages (PDF only, falls back to paragraph for other file types)
  - **Semantic**: Uses YOLO for segmentation and VLM + OCR for intelligent extraction of text, images, and tables — followed by semantic grouping for clean, contextual output.
  - **Paragraph**: Splits text by paragraphs
  - **Heading**: Splits text by identified headings

##  Technical Details

###  OpenAI Integration
- Uses OpenAI GPT-4o for semantic chunking
- Uses Unsiloed finetuned Yolo model for segmentation (https://huggingface.co/mubashiross/Unsiloed_YOLO_MODEL)
- Handles authentication via API key from environment variables
- Implements automatic retries and timeout handling
- Provides structured JSON output for semantic chunks

### Parallel Processing
- Multi-threaded processing for improved performance
- Parallel page extraction from PDFs
- Distributes processing of large documents across multiple threads

###  Document Processing
- Extracts text from PDF, DOCX, and PPTX files
- Handles image encoding for vision-based models
- Generates extraction prompts for structured data extraction

## Configuration

### Environmental Variables
- `OPENAI_API_KEY`: Your OpenAI API key

## Constraints & Limitations

### File Handling
- Temporary files are created during processing and deleted afterward
- Files are processed in-memory where possible

### Text Processing
- Long text (>25,000 characters) is automatically split and processed in parallel for semantic chunking
- Maximum token limit of 4000 for OpenAI responses

### API Constraints
- Request timeout set to 60 seconds
- Maximum of 3 retries for OpenAI API calls

##  Request Parameters

### Document Chunking Endpoint
- `document_file`: The document file to process (PDF, DOCX, PPTX)
- `strategy`: Chunking strategy to use (default: "semantic")
  - Options: "fixed", "page", "semantic", "paragraph", "heading"
- `chunk_size`: Size of chunks for fixed strategy in characters (default: 1000)
- `overlap`: Overlap size for fixed strategy in characters (default: 100)


##  Installation

### Using pip
```bash
pip install unsiloed
```


### Requirements
Unsiloed requires Python 3.8 or higher and has the following dependencies:
- openai
- PyPDF2
- python-docx
- python-pptx
- fastapi
- python-multipart

##  Environment Setup

Before using Unsiloed, set up your OpenAI API key:

### Using environment variables
```bash
# Linux/macOS
export OPENAI_API_KEY="your-api-key-here"

# Windows (Command Prompt)
set OPENAI_API_KEY=your-api-key-here

# Windows (PowerShell)
$env:OPENAI_API_KEY="your-api-key-here"
```

### Using a .env file
Create a `.env` file in your project directory:
```
OPENAI_API_KEY=your-api-key-here
```

Then in your Python code:
```python
from dotenv import load_dotenv
load_dotenv()  # This loads the variables from .env
```

## 🔍 Usage Example

### Python

```python
import os
import Unsiloed

# Example 1: Semantic chunking (default)
result = Unsiloed.process_sync({
    "filePath": "./test.pdf",
    "credentials": {
        "apiKey": os.environ.get("OPENAI_API_KEY")
    },
    "strategy": "semantic",
    "chunkSize": 1000,
    "overlap": 100
})

# Print the result
print(result)

# Example 2: Fixed-size chunking
fixed_result = Unsiloed.process_sync({
    "filePath": "./test.pdf", #path to your file
    "credentials": {
        "apiKey": os.environ.get("OPENAI_API_KEY")
    },
    "strategy": "fixed",
    "chunkSize": 1500,
    "overlap": 150
})

# Example 3: Page-based chunking (PDF only)
page_result = Unsiloed.process_sync({
    "filePath": "./test.pdf",
    "credentials": {
        "apiKey": os.environ.get("OPENAI_API_KEY")
    },
    "strategy": "page"
})

# Example 4: Paragraph chunking
paragraph_result = Unsiloed.process_sync({
    "filePath": "./document.docx",
    "credentials": {
        "apiKey": os.environ.get("OPENAI_API_KEY")
    },
    "strategy": "paragraph"
})

# Example 5: Heading chunking
heading_result = Unsiloed.process_sync({
    "filePath": "./presentation.pptx",
    "credentials": {
        "apiKey": os.environ.get("OPENAI_API_KEY")
    },
    "strategy": "heading"
})

# Example 6: Processing HTML files
html_result = Unsiloed.process_sync({
    "filePath": "./webpage.html",
    "credentials": {
        "apiKey": os.environ.get("OPENAI_API_KEY")
    },
    "strategy": "paragraph"
})

# Example 7: Processing Markdown files
markdown_result = Unsiloed.process_sync({
    "filePath": "./README.md",
    "credentials": {
        "apiKey": os.environ.get("OPENAI_API_KEY")
    },
    "strategy": "heading"
})

# Example 8: Processing website URLs
url_result = Unsiloed.process_sync({
    "filePath": "https://example.com",
    "credentials": {
        "apiKey": os.environ.get("OPENAI_API_KEY")
    },
    "strategy": "paragraph"
})



# Example 11: Using async version
import asyncio

async def async_processing():
    result = await Unsiloed.process({
        "filePath": "./test.pdf",
        "credentials": {
            "apiKey": os.environ.get("OPENAI_API_KEY")
        },
        "strategy": "semantic"
    })
    return result

# Run async processing
async_result = asyncio.run(async_processing())
```

### Supported File Types

- **PDF files** (.pdf) - All chunking strategies supported
- **Word documents** (.docx) - All strategies except page-based
- **PowerPoint presentations** (.pptx) - All strategies except page-based  
- **HTML files** (.html, .htm) - All strategies except page-based
- **Markdown files** (.md, .markdown) - All strategies except page-based
- **Website URLs** (http://, https://) - Automatically detected and processed

### Chunking Strategies

- **semantic**: AI-powered semantic chunking (requires OpenAI API key)
- **fixed**: Fixed-size chunks with configurable overlap
- **page**: Page-based chunking (PDF only)
- **paragraph**: Paragraph-based chunking
- **heading**: Heading-based chunking

### Credential Options

You can provide credentials in three ways:

1. **In the options** (recommended):
```python
result = Unsiloed.process_sync({
    "filePath": "./test.pdf",
    "credentials": {
        "apiKey": "your-openai-api-key"
    },
    "strategy": "semantic"
})
```

2. **Environment variable**:
```bash
export OPENAI_API_KEY="your-openai-api-key"
```

3. **Using .env file**:
```
OPENAI_API_KEY=your-openai-api-key
```

##  Development Setup

### Prerequisites

- Python 3.8 or higher
- pip (Python package installer)
- git

### Setting Up Local Development Environment

1. Clone the repository:
```bash
git clone https://github.com/Unsiloed-opensource/Unsiloed.git
cd Unsiloed
```

2. Create a virtual environment:
```bash
# Using venv
python -m venv venv

# Activate the virtual environment
# On Windows
venv\Scripts\activate
# On macOS/Linux
source venv/bin/activate
```

3. Install dependencies:
```bash
pip install -r requirements.txt
```

4. Set up your environment variables:
```bash
# Create a .env file
echo "OPENAI_API_KEY=your-api-key-here" > .env
```

5. Run the FastAPI server locally:
```bash
uvicorn Unsiloed.app:app --reload
```

6. Access the API documentation:
Open your browser and go to `http://localhost:8000/docs`



##  Contributing

We welcome contributions to Unsiloed! Here's how you can help:

### Setting Up Development Environment

1. Fork the repository and clone your fork:
```bash
git clone https://github.com/YOUR_USERNAME/Unsiloed.git
cd Unsiloed
```

2. Install development dependencies:
```bash
pip install -r requirements.txt
```


### Making Changes

1. Create a new branch for your feature:
```bash
git checkout -b feature/your-feature-name
```

2. Make your changes and write tests if applicable


4. Commit your changes:
```bash
git commit -m "Add your meaningful commit message here"
```

5. Push to your fork:
```bash
git push origin feature/your-feature-name
```

6. Create a Pull Request from your fork to the main repository

### Code Style and Standards

- We follow PEP 8 for Python code style
- Use type hints where appropriate
- Document functions and classes with docstrings
- Write tests for new features


##  License

This project is licensed under the Apache-2.0 License - see the [LICENSE](LICENSE) file for details.

##  Community and Support

### Join the Community

- **GitHub Discussions**: For questions, ideas, and discussions
- **Issues**: For bug reports and feature requests
- **Pull Requests**: For contributing to the codebase


### Staying Updated

- **Star** the repository to show support
- **Watch** for notification on new releases
