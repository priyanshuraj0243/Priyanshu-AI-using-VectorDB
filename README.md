# Priyanshu Raj's AI using VectorDB

A fully working Vector Database I built from scratch in C++ with a custom web UI. This project implements HNSW, KD-Tree, and Brute Force search algorithms side-by-side, along with a RAG pipeline powered by a local LLM via Ollama.

---

## Project Features

| Feature | Description |
|---|---|
| **3 Search Algorithms** | HNSW (production-grade), KD-Tree, Brute Force — run all three and compare speed |
| **3 Distance Metrics** | Cosine similarity, Euclidean distance, Manhattan distance |
| **16D Demo Vectors** | 20 pre-loaded semantic vectors across 4 categories (CS, Math, Food, Sports) |
| **2D PCA Scatter Plot** | Live visualization of semantic space — watch clusters form |
| **Real Document Embedding** | Paste any text → Ollama embeds it with `nomic-embed-text` (768D) |
| **RAG Pipeline** | Ask questions about your documents → HNSW retrieves context → local LLM answers |
| **Full REST API** | CRUD endpoints: insert, delete, search, benchmark, hnsw-info |

---

## Prerequisites

You need **3 things** installed on your Windows machine to run this project:

1. **MSYS2** (gives you the g++ compiler)
2. **Git**
3. **Ollama** (runs the local AI models)

---

## Step-by-Step Setup (Windows)

### Step 1 — Install MSYS2 (C++ Compiler)

1. Go to **https://www.msys2.org** and download the installer.
2. Run the installer, keep default path (`C:\msys64`).
3. After install, open **MSYS2 UCRT64** from Start Menu (the orange icon).
4. Run these commands inside the MSYS2 terminal:

```bash
pacman -Syu
```
*(Close and reopen the terminal if it asks you to)*

```bash
pacman -S mingw-w64-ucrt-x86_64-gcc
```

5. Add g++ to your Windows PATH:
   - Press `Win + R`, type `sysdm.cpl`, press Enter.
   - Click **Advanced** → **Environment Variables**.
   - Under **System variables**, find **Path**, click **Edit**.
   - Click **New** and add: `C:\msys64\ucrt64\bin`.
   - Click OK on all windows.
   - **Open a new PowerShell** and verify:
```powershell
g++ --version
```

### Step 2 — Install Git

1. Go to **https://git-scm.com/download/win** and download Git for Windows.
2. Verify in PowerShell:
```powershell
git --version
```

### Step 3 — Install Ollama (Local AI Models)

1. Go to **https://ollama.com** and click **Download for Windows**.
2. Run the installer.
3. Open **PowerShell** and pull the two required models:

```powershell
ollama pull nomic-embed-text
```

```powershell
ollama pull llama3.2
```

### Step 4 — Clone the Repository

Open **PowerShell** and run:

```powershell
git clone [https://github.com/priyanshuraj/VectorDB.git](https://github.com/priyanshuraj/VectorDB.git)
cd VectorDB
```

### Step 5 — Compile the C++ Server

Inside the `VectorDB` folder, run:

```powershell
g++ -std=c++17 -O2 main.cpp -o db -lws2_32
```

### Step 6 — Run Everything

**Terminal 1** — Start Ollama (if not already running in your system tray):
```powershell
ollama serve
```

**Terminal 2** — Start the VectorDB server:
```powershell
./db
```

**Open your browser** and go to:
```text
http://localhost:8080
```

---

## Using the Application

### Tab 1: Search (Demo Vectors)
- Type any concept in the search box: `binary tree`, `sushi`, `basketball`, `calculus`.
- Choose your algorithm: **HNSW**, **KD-Tree**, or **Brute Force**.
- Choose distance metric: **Cosine**, **Euclidean**, or **Manhattan**.
- Click **⚡ SEARCH** — results appear with distances, the matching point glows on the scatter plot.
- Click **▶ COMPARE ALL ALGOS** to run all 3 algorithms and compare their speed.

### Tab 2: Documents (Real Embeddings)
1. Type a title (e.g., `Operating Systems Notes`).
2. Paste any text — lecture notes, textbook paragraphs, Wikipedia articles.
3. Click **⚡ EMBED & INSERT**.
4. Long documents are automatically split into overlapping 250-word chunks.

### Tab 3: Ask AI (RAG Pipeline)
1. Make sure you have inserted some documents in Tab 2 first.
2. Type a question about your documents.
3. Click **🤖 ASK AI**.

The answer streams in with a typewriter effect. Click the **context chips** to see exactly which chunks the AI used.

---

## REST API Reference

The server exposes a full REST API at `http://localhost:8080`.

### Demo Vector Endpoints

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/search?v=f1,f2,...&k=5&metric=cosine&algo=hnsw` | K-NN search |
| `POST` | `/insert` | Insert a demo vector |
| `DELETE` | `/delete/:id` | Delete by ID |
| `GET` | `/items` | List all demo vectors |
| `GET` | `/benchmark?v=...&k=5&metric=cosine` | Compare all 3 algorithms |
| `GET` | `/hnsw-info` | HNSW graph structure and layer stats |
| `GET` | `/stats` | Database statistics |

### Document & RAG Endpoints

| Method | Endpoint | Body | Description |
|---|---|---|---|
| `POST` | `/doc/insert` | `{"title":"...","text":"..."}` | Embed and store document |
| `GET` | `/doc/list` | — | List all stored documents |
| `DELETE` | `/doc/delete/:id` | — | Delete document chunk |
| `POST` | `/doc/ask` | `{"question":"...","k":3}` | RAG: retrieve + generate |
| `GET` | `/status` | — | Ollama status and model info |

---

## Project Structure

```text
VectorDB/
├── main.cpp        ← C++ backend (HNSW, KD-Tree, BruteForce, REST API, RAG)
├── httplib.h       ← Single-header HTTP server library (cpp-httplib)
├── index.html      ← Frontend (PCA scatter plot, chat UI, benchmark)
└── README.md       ← This file
```

---

## Common Issues

| Problem | Fix |
|---|---|
| `Ollama: OFFLINE` in header | Run `ollama serve` in a terminal |
| Embedding takes forever | Ollama is downloading the model on first use, wait 2 min |
| `g++: command not found` | Add `C:\msys64\ucrt64\bin` to Windows PATH |
| Port 8080 already in use | Kill the process: `netstat -ano \| findstr 8080` then `taskkill /PID <pid> /F` |
| LLM answer is slow | Normal — llama3.2 takes 10–30s on a laptop CPU. Use llama3.2:1b for faster answers |

---

## License

MIT — use this however you want.
