# Tech News Summarizer — n8n Automation

## Overview

An automated workflow built in **n8n** that monitors TechCrunch RSS feed, filters technology and AI articles, summarizes them using **Groq AI (LLaMA 3.3-70B)**, and produces clean structured output.

---

## Workflow

| # | Trigger | Description |
|---|---|---|
| 1 | RSS Feed | Auto-triggers on new TechCrunch articles, filters AI/Tech content, summarizes using Groq AI |

---

## Workflow Architecture

```
RSS Feed Trigger (TechCrunch)
        ↓
Filter Node (title contains "AI" OR "Tech")
        ↓
HTTP Request (Fetch article content)
        ↓
Code Node (Build Groq API request body)
        ↓
HTTP Request → Groq AI API (LLaMA 3.3-70B)
        ↓
Code Node (Parse and format AI response)
        ↓
Output: Structured JSON with 3 bullet point summary
```

---

## Tech Stack

| Component | Tool |
|---|---|
| Automation Platform | n8n (Docker) |
| AI Model | Groq — LLaMA 3.3-70B Versatile |
| News Source | TechCrunch RSS Feed |
| News API | NewsAPI.org |
| Language | JavaScript (n8n Code nodes) |

---

## Setup Instructions

### Prerequisites
- Docker installed
- n8n running locally (`http://localhost:5678`)
- Free Groq API key from [console.groq.com](https://console.groq.com)
- Free NewsAPI key from [newsapi.org](https://newsapi.org)

### 1. Start n8n with Docker
```bash
docker run -it --name n8n -p 5678:5678 n8nio/n8n
```

### 2. Import Workflow
1. Open `http://localhost:5678`
2. Click **Workflows** → **Import**
3. Upload `workflows/1-rss-news-summarizer.json`

### 3. Configure Credentials
Add these in n8n → Credentials:

**Groq AI (via OpenAI node):**
- Type: OpenAI
- API Key: your Groq key from console.groq.com
- Base URL: `https://api.groq.com/openai/v1`

**HTTP Authorization Header (for direct Groq calls):**
- Type: Header Auth
- Name: `Authorization`
- Value: `Bearer YOUR_GROQ_KEY`

### 4. Activate Workflow
- Open the imported workflow
- Click the **Activate** toggle (top right)
- Workflow will now poll TechCrunch every 15 minutes

---

## How to Test Manually

1. Open workflow in n8n
2. Click **RSS Feed Trigger** node → **Fetch Test Event**
3. Click **Execute Workflow** button
4. Click the last Code node → **Table** tab to see output

---

## Sample Output

```json
[
  {
    "bullet_no": 1,
    "summary": "The article discusses using ChatGPT to generate content and drive free traffic to websites, comparing AIO and SEO strategies.",
    "source": "TechCrunch",
    "generated_at": "2026-06-17T09:27:20.829Z"
  },
  {
    "bullet_no": 2,
    "summary": "ChatGPT can be leveraged to produce high-quality, SEO-optimized content that attracts search engine traffic.",
    "source": "TechCrunch",
    "generated_at": "2026-06-17T09:27:20.829Z"
  },
  {
    "bullet_no": 3,
    "summary": "By utilizing ChatGPT's content generation capabilities, users can potentially increase their online visibility and drive free traffic.",
    "source": "TechCrunch",
    "generated_at": "2026-06-17T09:27:20.829Z"
  }
]
```

---

## Screenshots

See `/screenshots/` folder:
- `workflow-canvas.png` — Full workflow with all green nodes
- `ai-output-table.png` — Table output showing AI bullet summaries
- `groq-json-response.png` — Raw Groq API JSON response

---

## Repository Structure

```
n8n-tech-news-summarizer/
│
├── workflows/
│   └── 1-rss-news-summarizer.json   ← Import this into n8n
│
├── outputs/
│   └── sample-output.json           ← Sample AI summary output
│
├── screenshots/
│   ├── workflow-canvas.png
│   ├── ai-output-table.png
│   └── groq-json-response.png
│
└── README.md
```

---

## Design Decisions

- **Single theme across trigger:** RSS feed chosen as primary trigger for continuous automated monitoring without manual intervention.
- **Groq over OpenAI:** Groq is free with higher rate limits (14,400 req/day) and faster inference — ideal for automation projects.
- **LLaMA 3.3-70B:** Best quality free model available on Groq as of June 2026.
- **Filter before AI call:** Reduces unnecessary API calls by only processing relevant tech/AI articles.
- **Structured bullet output:** Ensures output is always readable, consistent, and usable downstream.

---

## Known Limitations

- Docker container `/data` directory requires volume mount for persistent file writes. Output is currently captured from node panel and downloaded manually.
- RSS feed polling depends on TechCrunch publishing new articles matching the filter keywords.

---

## Author

**Saif Ullah Khalid**  
Automation Assessment — Al Rafay Consulting  
Date: June 2026
