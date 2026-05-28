<div align="center">

# 🕌 Visual RAG — Moroccan Landmarks Identification

**AI-Powered Visual Search & Tourist Guide for Moroccan Heritage Sites**

<p>
  <img src="https://img.shields.io/badge/Python-3.9+-3776AB?style=flat-square&logo=python&logoColor=white" />
  <img src="https://img.shields.io/badge/Streamlit-App-FF4B4B?style=flat-square&logo=streamlit&logoColor=white" />
  <img src="https://img.shields.io/badge/CLIP-ViT--B--32-blueviolet?style=flat-square" />
  <img src="https://img.shields.io/badge/ChromaDB-Vector_Store-orange?style=flat-square" />
  <img src="https://img.shields.io/badge/Wikipedia-Fallback_Search-grey?style=flat-square&logo=wikipedia" />
  <img src="https://img.shields.io/badge/Ollama-Local_LLM-black?style=flat-square" />
  <img src="https://img.shields.io/badge/License-MIT-green?style=flat-square" />
</p>

*Upload a photo of a Moroccan landmark — get instant AI identification, historical context, and a full tourist guide, in French, English, or Darija.*

</div>

---

## Table of Contents

1. [Overview](#overview)
2. [AI Solution](#ai-solution)
3. [System Architecture](#system-architecture)
4. [Covered Landmarks](#covered-landmarks)
5. [Features](#features)
6. [Tech Stack](#tech-stack)
7. [Getting Started](#getting-started)
8. [Usage](#usage)
9. [Project Structure](#project-structure)
10. [Results & Metrics](#results--metrics)
11. [Roadmap](#roadmap)
12. [Team & Contact](#team--contact)

---

## Overview

Morocco is home to extraordinary heritage sites — ancient Roman ruins, medieval medersas, UNESCO-listed ksars, and iconic minarets. Yet identifying them from a photo remains a challenge for tourists and developers alike.

**Visual RAG — Moroccan Landmarks** is a Retrieval-Augmented Generation (RAG) system that combines visual embeddings, a local vector database, and a locally-hosted language model to **identify Moroccan landmarks from photos and generate rich, multilingual tourist guides** — no cloud API required.

When a landmark is outside the local dataset, the system **automatically falls back to Wikipedia search**, ensuring no photo goes unanswered.

---

## AI Solution

**What the system does:**  
The pipeline encodes any uploaded image using the CLIP ViT-B/32 vision model, performs cosine similarity search against a ChromaDB vector store of indexed landmark photos, and routes the result to a local LLM (via Ollama) to generate a contextual tourist guide.

**Why it's innovative:**  
- **Fully local** — CLIP + ChromaDB + Ollama run entirely on-device; no OpenAI, no cloud fees.  
- **Graceful degradation** — if the photo doesn't match anything in the local base (similarity below threshold), the system queries Wikipedia automatically using LLM-inferred keywords, then generates a guide from the retrieved article.  
- **Multilingual** — responses are generated natively in French, English, or Darija (Moroccan Arabic dialect).  
- **Confidence-aware** — every identification comes with a calibrated confidence badge (Confident / Probable / Uncertain / Unknown).

---

## System Architecture

```
User Photo (JPG/PNG)
        │
        ▼
┌───────────────────┐
│  CLIP ViT-B/32    │  ← Encodes image into a 512-dim embedding
│  (SentenceTransf.)│
└────────┬──────────┘
         │
         ▼
┌───────────────────┐         ┌──────────────────────────┐
│  ChromaDB         │◄────────│  Indexed Local Dataset   │
│  Vector Store     │  cosine │  (10 images, 5 landmarks)│
└────────┬──────────┘ search  └──────────────────────────┘
         │
     Score ≥ Threshold?
    ┌────┴────┐
   YES        NO
    │          │
    ▼          ▼
Local      Wikipedia API
Metadata   (FR → EN fallback)
    │          │
    └────┬─────┘
         │
         ▼
┌───────────────────┐
│  Ollama LLM       │  ← Generates tourist guide in chosen language
│  (qwen/mistral/   │
│   llama3 etc.)    │
└───────────────────┘
         │
         ▼
  Streamlit Dashboard
```

---

## Covered Landmarks

The local dataset covers **5 iconic Moroccan heritage sites** with 10 reference images:

| Landmark | City | UNESCO | Description |
|---|---|---|---|
| **Tour Hassan** | Rabat | — | Unfinished minaret of a 12th-century Almohad mosque; symbol of Rabat |
| **Volubilis** | Meknès region | ✅ 1997 | Partially excavated Berber-Roman city; capital of the Mauritania kingdom |
| **Cascades de Sefrou** | Sefrou | — | Waterfalls formed by the Aggaï river; gateway to one of Morocco's oldest cities |
| **Aït Benhaddou** | Ouarzazate region | ✅ 1987 | Exceptional ksar on the ancient caravan route; backdrop to major film productions |
| **Medersa Bou Inania** | Fès | — | Merinid Quranic school (1351–1356); masterpiece of Hispano-Moorish architecture |

> The system automatically extends beyond this list via Wikipedia fallback for any unrecognized landmark.

---

## Features

### 🔍 Visual Identification
- Upload a JPG/PNG photo or use your device camera directly
- CLIP encodes the image; ChromaDB finds the closest landmark by cosine similarity
- Side-by-side display of your photo vs. the dataset reference image

### 🏷️ Confidence Badges
- **Confident** (score ≥ threshold) — high-confidence local match
- **Probable** — moderate match, guide generated with caveats
- **Uncertain** — low match, Wikipedia fallback triggered
- **Wikipedia** — result sourced entirely from web search

### 🌍 Multilingual Tourist Guides
Generated guides follow a structured format: **presentation → history → anecdote → visit tip**, natively in:
- 🇫🇷 **French**
- 🇬🇧 **English**
- 🇲🇦 **Darija** (Moroccan Arabic dialect)

### 📖 Wikipedia Fallback
When similarity falls below the threshold, the system:
1. Asks the LLM to infer a search keyword from the photo
2. Queries the Wikipedia API (French first, English fallback)
3. Generates a guide from the retrieved article extract

### 📊 Statistics & Evaluation Dashboard
- Session history (local matches vs. Wikipedia hits)
- Dataset browser with all indexed images
- Precision@1 auto-evaluation over the full local dataset

### 📥 Export
- Download any generated guide as a `.txt` file

---

## Tech Stack

| Component | Technology |
|---|---|
| **Visual Embedding** | CLIP `ViT-B/32` via `sentence-transformers` |
| **Vector Store** | ChromaDB (persistent, cosine similarity) |
| **Language Models** | Ollama — `qwen2.5:1.5b`, `mistral`, `llama3`, `llama3.2` |
| **Web Fallback** | Wikipedia REST API (FR / EN) |
| **Frontend** | Streamlit |
| **Image Processing** | Pillow (PIL) |
| **Language** | Python 3.9+ |

---

## Getting Started

### Prerequisites

- Python 3.9+
- [Ollama](https://ollama.com/) installed and running locally
- At least one Ollama model pulled (e.g. `ollama pull qwen2.5:1.5b`)

### Installation

```bash
git clone https://github.com/<your-username>/Visual-RAG-Moroccan-Landmarks-Identification.git
cd Visual-RAG-Moroccan-Landmarks-Identification

pip install -r requirements.txt
```

**Key dependencies:**
```
streamlit
sentence-transformers
chromadb
pillow
pandas
requests
```

### Launch

```bash
streamlit run app.py
```

The app opens at `http://localhost:8501`.

---

## Usage

### 1. Sidebar — Configuration
- **LLM Model**: select the Ollama model to use for guide generation
- **Language**: choose French, English, or Darija
- **Similarity Threshold**: controls when Wikipedia fallback activates (default: 0.40)
- **Re-index**: re-encode the local dataset into ChromaDB if images change
- **Dark Mode**: toggle the UI theme

### 2. Identification Tab
1. Upload a photo (JPG/PNG) or take one with your camera
2. Optionally enter a landmark name to help Wikipedia search
3. Click **Identifier** — the system searches locally, then falls back to Wikipedia if needed
4. View the confidence badge, reference comparison, and generated tourist guide
5. Export the guide as a `.txt` file

### 3. Available Sites Tab
Browse all 5 indexed landmarks with their reference photos and metadata.

### 4. Statistics Tab
Review session performance: total queries, local hits, Wikipedia fallbacks, and Precision@1 on the full dataset.

---

## Project Structure

```
Visual-RAG-Moroccan-Landmarks-Identification/
│
├── app.py                              # Main Streamlit application (807 lines)
├── metadata.json                       # Landmark metadata (name, city, history, image refs)
│
├── dataset/                            # Reference images by landmark
│   ├── tour_hassan/
│   ├── volubilis/
│   ├── cascades_sefrou/
│   ├── ait_benhaddou/
│   └── medersa_bou_inania/
│
├── chroma_db/                          # Persistent ChromaDB vector store
│   └── ...
│
└── visual_rag_tourisme_COMPLET.ipynb  # Full experimentation notebook
```

---

## Results & Metrics

| Metric | Value |
|---|---|
| **Local dataset size** | 5 landmarks · 10 reference images |
| **Embedding model** | CLIP `ViT-B/32` (512-dim) |
| **Similarity metric** | Cosine |
| **Default fallback threshold** | 0.40 |
| **Wikipedia fallback** | FR → EN automatic |
| **Supported LLMs** | 4 (qwen2.5:1.5b, mistral, llama3, llama3.2) |
| **Languages** | 3 (French, English, Darija) |

> Precision@1 on the local dataset is computed live in the Statistics tab after a full evaluation run.

---

## Roadmap

- [x] CLIP visual embedding + ChromaDB local vector store
- [x] Wikipedia API fallback (FR + EN)
- [x] Multilingual guide generation (French, English, Darija)
- [x] Confidence badges and similarity threshold control
- [x] Camera input support
- [x] Export to `.txt`
- [ ] Expand dataset to 20+ Moroccan landmarks
- [ ] Add GPS coordinates and interactive map (Folium/Leaflet)
- [ ] Fine-tune CLIP on a dedicated Moroccan heritage dataset
- [ ] Docker containerization for easy deployment
- [ ] Streamlit Cloud deployment (public demo)
- [ ] Support for additional Maghrebi languages (Tamazight)

---

## Contributing

Contributions are welcome! Please use **Conventional Commits**:

```text
feat: add new landmark to dataset
fix: improve Wikipedia fallback parsing
docs: update architecture diagram
```

---

## Team & Contact

Built with ❤️ for Moroccan cultural heritage and AI exploration.

For academic inquiries, collaboration, or feedback, open an Issue or reach out directly.

---

<div align="center">
  <sub>Distributed under the MIT License · Powered by CLIP, ChromaDB, Ollama & Streamlit</sub>
</div>
