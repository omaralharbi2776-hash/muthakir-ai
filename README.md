# Muthakir AI

A PDF study assistant built with Python, Streamlit, and Google Gemini. Upload lecture PDFs, ask questions about their content, generate summaries, and create practice questions with model answers.

## Features

- English interface with a dark theme.
- Upload and process multiple text-based PDFs.
- Ask questions using semantic search across document chunks.
- View the filenames and page numbers of retrieved sources.
- Generate a lecture summary with key terms and definitions.
- Generate five practice questions with answers in a separate section.
- Keep questions and answers visible during the current session.
- Download the session notes as a text file.
- Display helpful messages for quota, availability, and access errors.

Answers follow the language of the question. The summary and practice-question buttons request English output.

## How it works

1. PyPDF2 extracts text from each PDF page.
2. A recursive text splitter creates overlapping chunks of up to 1,800 characters, with 200-character overlap.
3. Gemini embeddings represent each chunk numerically, and FAISS stores the vectors in memory.
4. For a question, the app retrieves four relevant chunks and sends them with the question to Gemini.
5. Gemini is instructed to answer from the supplied text and acknowledge missing information.

This approach is called Retrieval-Augmented Generation (RAG). It uses an existing model; it does not train or fine-tune one. Summary and practice-question requests use the extracted text directly, limited to its first 60,000 characters.

## Technology

| Component | Tool |
| --- | --- |
| Interface | Streamlit |
| PDF extraction | PyPDF2 |
| Text splitting | langchain-text-splitters |
| Vector search | FAISS through langchain-community |
| Gemini integration | langchain-google-genai |
| Local configuration | python-dotenv |

The current code uses `gemini-3.1-flash-lite` for answers and `models/gemini-embedding-001` for embeddings. Model access and quotas depend on your Google project and can change.

## Run locally on Windows

The application was exercised on Windows with Python 3.12.10. You need Python, an internet connection, and a Gemini API key with access to the configured models.

### 1. Open the project

Download this repository as a ZIP and extract it, or clone your own repository. Open the folder containing `chatapp.py` in VS Code and open a terminal there.

### 2. Create a virtual environment and install dependencies

```powershell
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
```

These commands use the environment directly; activation is not required. Dependencies are currently unpinned, so a new installation can receive different versions from the tested local environment.

### 3. Configure your API key

Create a key in [Google AI Studio](https://aistudio.google.com/apikey). Create a file named `.env` beside `chatapp.py`:

```dotenv
GOOGLE_API_KEY=your_api_key_here
```

Replace the placeholder locally. Never commit your actual key. The supplied `.gitignore` excludes `.env`, environment folders, and local generated data. Files already tracked by Git are not protected by adding ignore rules; check staged files before committing. When uploading through the GitHub website, manually select only the intended project files.

### 4. Start the app

```powershell
.\.venv\Scripts\python.exe -m streamlit run chatapp.py
```

Open the Local URL printed in the terminal if the browser does not open automatically.

## Usage

1. Upload one or more PDFs under **Lecture files**.
2. Click **Process files** and wait for completion.
3. Enter a question and click **Ask Muthakir**, or choose **Summarize lecture** or **Practice questions**.
4. Use **Download notes** to save the session results.

Process files again when you change the selected documents. You do not need to process them again for every question.

## Limitations and data handling

- Files, the in-memory search index, and conversation history last only for the page session. Refreshing the browser requires uploading and processing the PDFs again.
- Previous answers are displayed as history but are not sent as conversation context. Ask self-contained questions.
- Scanned image-only PDFs need OCR, which this app does not provide. Text extraction, especially from complex layouts, can be imperfect.
- Summaries and practice questions cover only the first 60,000 extracted characters. The app shows a warning when it truncates the text.
- Retrieved page references identify search results, not independently verified citations for every generated claim.
- Extracted text and questions are sent to Google services. Use documents you are permitted to share.
- Generated answers can be incorrect. Check them against the original material.
- This is a local learning project. Additional work is needed before operating it as a public multi-user service.

## Troubleshooting

| Message | Next step |
| --- | --- |
| Missing API key | Check `.env` beside `chatapp.py`, save it, and restart the app. |
| No extractable text | Try a text-based PDF with selectable text. |
| Usage limit reached (429) | Check your project quota in Google AI Studio and wait for the relevant limit to reset. Re-uploading will not fix it. |
| Service busy (503) | Try again later. |
| Model unavailable (404) | Verify model access in your project before changing the model name. |
| Buttons disabled | Upload and process the selected files and confirm the API key is configured. |

The answer model is configured with automatic retries disabled to avoid repeated generation attempts after an error.

## Validation

The local walkthrough verified PDF processing, question answering in the original setup, and summary generation, practice questions, and downloaded notes in the updated app. Python syntax and selected helper functions were also checked. This is not a full compatibility or production test suite; a clean installation with pinned dependency versions has not been verified.

## Changes from the original project

- Rebranded as Muthakir AI with an English study-focused interface.
- Updated imports and model configuration used in the local walkthrough.
- Added summary generation, practice questions, session history, and note downloads.
- Added explicit question submission and error messages.
- Replaced disk-based pickle index loading with an in-memory session index.
- Added page metadata and file-selection checks.
- Updated setup instructions and dependency/ignore files.

## Attribution and license

Adapted from [Multi-PDFs_ChatApp_AI-Agent](https://github.com/GURPREETKAURJETHRA/Multi-PDFs_ChatApp_AI-Agent) by [Gurpreet Kaur Jethra](https://github.com/GURPREETKAURJETHRA).

The original project is distributed under the MIT License. Retain its original copyright and permission notice in the `LICENSE` file when sharing this adaptation. This repository is a modified version, not an entirely original implementation.
