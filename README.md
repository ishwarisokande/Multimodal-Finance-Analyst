# Multimodal Finance Analyst

An experimental, notebook-based financial report question-answering project. It extracts text from an NVIDIA quarterly presentation PDF, indexes that text in a local Chroma vector database, and uses Google Gemini to answer questions with page references.

> **Current scope:** Despite the project name, the notebook currently processes text extracted from a PDF. It does not yet analyze charts, images, audio, or other non-text inputs.

## Features

- Extracts page text from the NVIDIA Q4 FY2025 quarterly presentation using `pdfplumber`.
- Splits extracted text into overlapping chunks for retrieval.
- Creates embeddings with Google's Gemini embedding model and stores them locally in Chroma.
- Retrieves relevant report passages for a question.
- Generates answers with Gemini, constrained to the retrieved report context and asked to include source page numbers.
- Demonstrates both a basic retrieval-and-answer flow and a LangGraph workflow, with an interactive question loop.

## Project files

| File | Description |
|---|---|
| [`multimodal (1).ipynb`](./multimodal%20(1).ipynb) | Notebook containing document ingestion, retrieval, and question-answering code. |
| [`NVDA-F4Q25-Quarterly-Presentation-FINAL.pdf`](./NVDA-F4Q25-Quarterly-Presentation-FINAL.pdf) | PDF source document that the notebook reads. |
| `chroma_db/` | Local vector database created when the notebook persists indexed document chunks. This directory is generated at runtime. |

## Requirements

- Python 3.10 or later (a Python version supported by the installed LangChain, Chroma, and Google AI packages).
- Jupyter Notebook support, such as the Jupyter extension in Visual Studio Code.
- A Google Gemini API key with access to the configured Gemini models.
- The presentation PDF in the same directory as the notebook.

## Setup

1. Clone or download the project and open its directory in VS Code.
2. Create and activate a virtual environment:

   **Windows (PowerShell):**

   ```powershell
   py -m venv .venv
   .\.venv\Scripts\Activate.ps1
   ```

   **macOS/Linux:**

   ```bash
   python3 -m venv .venv
   source .venv/bin/activate
   ```

3. Install the notebook dependencies:

   ```bash
   python -m pip install --upgrade pip
   python -m pip install jupyter ipykernel pdfplumber langchain langchain-text-splitters langchain-google-genai langchain-chroma chromadb langgraph
   ```

4. In VS Code, open [`multimodal (1).ipynb`](./multimodal%20(1).ipynb) and select the virtual environment as the notebook kernel.
5. Run the cells from top to bottom. The notebook prompts for a Gemini API key using `getpass`; enter the key when prompted. Do not put API keys directly into the notebook or commit them to source control.

The notebook includes package-installation cells as well. If you have already installed the packages using the command above, you can skip those cells.

## How it works

1. **Load the source:** The notebook opens `NVDA-F4Q25-Quarterly-Presentation-FINAL.pdf` and extracts text page by page.
2. **Prepare the text:** Pages are split into chunks of approximately 500 characters with 100 characters of overlap. Page metadata is retained for citations.
3. **Embed and index:** Gemini embeddings are generated for the chunks and stored in the `financial_reports` Chroma collection under `./chroma_db`.
4. **Retrieve context:** The notebook searches the vector store for passages relevant to a question.
5. **Generate an answer:** Gemini is prompted to use only the retrieved context, say when the report does not contain an answer, and mention source page(s).

## Using the notebook

Run the cells in order to build the vector index and initialize the model. The notebook includes sample questions such as:

- What was NVIDIA's total Q4 FY25 revenue?
- What is NVIDIA Blackwell?
- What happened in the automotive segment?
- What was gaming revenue?

You can also use the interactive question loop near the end of the notebook. Enter `exit` to stop.

## Notes and limitations

- Answers depend on the PDF text extraction and the passages retrieved; verify important figures against the original presentation.
- The assistant is prompted to provide report page references, but generated answers may still be incomplete or incorrect.
- The current document-processing flow is text-only. It may not capture information conveyed only by charts, graphics, or images.
- The notebook defines a query-rewriting function, but the currently compiled LangGraph connects retrieval directly to answer generation; query rewriting is not part of that graph yet.
- The Chroma database is persisted locally. If you change the source PDF, rebuild the index from a clean `chroma_db/` directory to avoid mixing old and new chunks.
- This project is for educational and informational use only. It is not investment, legal, tax, or accounting advice.

## Troubleshooting

- **PDF not found:** Keep `NVDA-F4Q25-Quarterly-Presentation-FINAL.pdf` in the same directory as the notebook, or update the `pdf_path` variable in the notebook.
- **Authentication or model-access error:** Confirm that your Gemini API key is valid and that your account can access the embedding and chat models configured in the notebook.
- **Import error:** Ensure the selected notebook kernel is the same Python environment where you installed the dependencies.
- **Unexpected or stale retrieval results:** Rebuild the local Chroma index after changing the PDF. Do not reuse an index built from a different version of the report.

## Author

Add your name here.
