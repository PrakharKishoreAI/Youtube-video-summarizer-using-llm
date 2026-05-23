# 🎬 YouTube Video Summarizer using LLM

A Kaggle-based NLP project that automatically fetches transcripts from YouTube videos and generates structured summaries using a Large Language Model (LLaMA 3.3 via Groq API).

---

## 📌 Project Overview

This project takes a YouTube video URL as input, extracts the spoken transcript using `pytubefix`, and summarizes it into key points using the **LLaMA-3.3-70B** model served via the **Groq API** — all running inside a Kaggle notebook.

---

## 🚀 Demo Output

**Input Video:** [Classification in ML — Santi](https://www.youtube.com/watch?v=7owOKKgT0ak)

**Generated Summary:**
```
1. Brief Summary
   The transcript discusses binary classification in machine learning,
   explaining the math and decision boundary derivation.

2. Key Topics
   - Binary classification and target variable (Y)
   - Decision boundary visualization
   - Log likelihood and logistic regression

3. Main Takeaways
   - Classification predicts Y based on input features X1, X2
   - Decision boundary separates the two classes
   - Log likelihood is core to understanding the math behind classification
```

---

## 🛠️ Tech Stack

| Tool | Purpose |
|------|---------|
| `pytubefix` | Fetch YouTube captions/transcripts |
| `Groq API` | LLM inference (LLaMA 3.3 70B) |
| `Kaggle Notebooks` | Execution environment |
| `Python 3.12` | Core language |

---

## 📁 Project Structure

```
youtube-summarization-using-llm/
│
├── youtube-summarization-using-llm.ipynb   # Main Kaggle notebook
└── README.md
```

---

## ⚙️ Setup & Usage

### 1. Clone / Open on Kaggle
Open the notebook directly on Kaggle or clone this repo.

### 2. Add Groq API Key as Kaggle Secret
- Go to **Add-ons → Secrets** in your Kaggle notebook
- Add a new secret:
  - **Label:** `GROQ_KEY`
  - **Value:** your Groq API key (get it free at [console.groq.com](https://console.groq.com))
- Toggle it **ON** for the notebook

### 3. Install Dependencies
```python
!pip install pytubefix groq -q
```

### 4. Run All Cells
The notebook will:
1. Fetch the transcript from the YouTube video
2. Clean and preprocess the text
3. Send it to LLaMA 3.3 via Groq for summarization
4. Print a structured summary

---

## 🧠 How It Works

```
YouTube URL
    ↓
pytubefix → fetch captions (SRT format)
    ↓
Regex cleaning → plain transcript text
    ↓
Groq API (LLaMA-3.3-70B) → structured summary
    ↓
Output: Brief Summary + Key Topics + Takeaways
```

---

## 📝 Code Walkthrough

**Fetch Transcript**
```python
from pytubefix import YouTube
import re

yt = YouTube("https://www.youtube.com/watch?v=7owOKKgT0ak")
caption = yt.captions.get_by_language_code('en') or yt.captions.get_by_language_code('a.en')
raw = caption.generate_srt_captions()
text = re.sub(r'\d+\n\d{2}:\d{2}:\d{2},\d+ --> \d{2}:\d{2}:\d{2},\d+\n', '', raw)
transcript = ' '.join(text.strip().split())
```

**Summarize with Groq**
```python
from groq import Groq
from kaggle_secrets import UserSecretsClient

client = Groq(api_key=UserSecretsClient().get_secret("GROQ_KEY"))

response = client.chat.completions.create(
    messages=[{"role": "user", "content": f"Summarize: {transcript[:3000]}"}],
    model="llama-3.3-70b-versatile",
    max_tokens=512
)
print(response.choices[0].message.content)
```

---

## 🔑 API Keys Required

| Service | Free Tier | Link |
|---------|-----------|------|
| Groq | ✅ Yes | [console.groq.com](https://console.groq.com) |

---

## 📌 Notes

- Works best with videos that have **English captions** (manual or auto-generated)
- Transcript is truncated to **3000 characters** before sending to LLM (can be increased)
- `DeprecationWarnings` from Kaggle's environment are harmless and can be ignored

---

## 👤 Author

**Prakhar Kishore**

- LinkedIn: [linkedin.com/in/prakhar-kishore-130b30266](https://linkedin.com/in/prakhar-kishore-130b30266)

---

## 📄 License

This project is open source and available under the [MIT License](LICENSE).
