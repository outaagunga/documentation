
## Mini Grammarly- Like App powered by Python (FastAPI), React, and Bootstrap
The app will be hosted 100% free using Render + GitHub Pages (or Vercel).

Let’s design your **full project blueprint** from backend → frontend → hosting 👇

---

## 🧩 1. PROJECT OVERVIEW

**Name:** *WriteWise* (you can rename it later)
**Goal:** Provide grammar, readability, and style and clarity, tone adjustment, sentence rewrites suggestions for any written text.
**Stack:**

* **Backend:** Python + FastAPI
* **Frontend:** React + Bootstrap
* **Grammar Engine:** `language_tool_python` (offline grammar checker)
* **Readability Engine:** `textstat`
* **AI Rewriter:** OpenAI or HuggingFace API (optional add-on)

## Setting up a Python development environment:
This ensures you have a consistent and isolated development space for your projects:
a) installing Python
Download the latest version of ```Python``` from the official website
During installation (when using Windows), ensure you check the box to "```Add Python to PATH```" so you can
run Python commands from the command line. 
If you are using windows, then also ensure you install ```WSL``` (Windows Subsystem for Linux) - this lets you run ```linux commands``` directly from windows

b) Install an IDE (e.g VS Code), 
c) Install VS Code extensions e.g (prettier formatters, autocomplete and extensions)
d) Install AI helper (e.g Copilot, Codium extension)
e) Install a virtual environment manager e.g venv
f) Install python extension on the vs code to be able to run python directly from the vs code. Then click  ▶️ to run python


## Set up a Virtual Environment: (Why virtual environments?)
They isolate project dependencies, preventing conflicts between different projects and ensuring your code runs consistently.
 
### Tools for managing virtual environments:
- venv (built-in in Python 3.3+)
- virtualenv (third-party library)

## How to create and activate virtual environment
### Option I:
- Create project folder and open the folder in your terminal
- Launch virtual environment using command: ```pip install pipenv``` (if you get an error use ```sudo apt install pipenv```)
- Then activate the virtual environment using command: ```pipenv shell```
if you get error, unstall the older version of pipenv using this command:  ```sudo apt remove pipenv```

Then re- install new version using this command:
- ```pip install --user pipenv```
- Then ```sudo apt install pipenv```
Then re-activate virtual environment using command:  ```pipenv shell```


### Option II:
Create project folder and open the folder in your terminal
Launch virtual environment using command: ```python3 -m venv venv```
Then activate the virtual environment using command:
```.\venv\Scripts\activate #(for windows)```
or
```source venv/bin/activate #(for mac/ linux os)```

## Then create requirements.txt file. 
### Option I:
- Use command:   ```pip freeze > requirements.txt```
- To install everything listed in the requirements.txt, 
Use command:   ```pip install -r .\requirements.txt```

## Nb:
If you run into version compatibility issues while working on your project, simply install the available version first. Then, update the version in your ```requirements.txt``` file to the one compatible with your project, and reinstall the dependencies using the updated requirements.txt

### Option II:
Install pipreqs using this command: 
```pip install pipreqs``` 
Pipreqs is a tool that automatically generates a requirements.txt file for your Python project by scanning your project folder for all the libraries your code is importing.

Then run the pipreqs tool on the current directory using this command:
```pipreqs . ```
Then install all the Python packages listed inside your requirements.txt file using this command:
```pip install -r requirements.txt```


## Install Dependencies
(Frameworks that you will use for your project e,g Faker, Pandas, Django, Rest, Flask e.t.c).
Use ```pip``` which is the recommended Python's package installer. Ensure you do this after activating your virtual environment.

## In our case, we are going to use:
- `fastapi` to build Backend API
- `uvicorn` as the ASGI server to run FastAPI
- `language-tool-python` for grammar and spelling correction
- `textstat` for readability scoring
- `vaderSentiment` for tone and sentiment analysis
- `transformers` and `torch` for text summarization using the BART model

We are going to install all the libraries using pip command e.g:  
```bash
pip install --upgrade pip
pip install <dependency-name>  
```

To be able to run language-too-python on ubuntu, you need to also install `Java` version 8+  
```bash
sudo apt install default-jre
```
if you need specific version of Java, un-install the version installed using the command:
```bash
sudo apt remove default-jre
sudo apt autoremove
```
then install the needed version by specifying the version using command e.g
```bash
sudo apt update
sudo apt install openjdk-21-jre
```
Confirm if installation is successful using command `java -version

---

## 🧱 2. PROJECT STRUCTURE

```
writewise/
│
├── backend/
│   ├── app.py
│   ├── requirements.txt
│   ├── Dockerfile (optional for Render)
│
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── components/
│   │   │   ├── Editor.js
│   │   │   ├── ResultCard.js
│   │   │   └── Navbar.js
│   │   ├── App.js
│   │   └── index.js
│   └── package.json
│
└── README.md
```

---

## ⚙️ 3. BACKEND (FastAPI)

### `app.py`

You can implement all features under one `/analyze` endpoint — each module runs independently, often in parallel to save time.

Here’s a **blueprint example:**

```python
from fastapi import FastAPI, Request
from fastapi.middleware.cors import CORSMiddleware
import asyncio, textstat
from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer
import language_tool_python
from transformers import pipeline
from functools import lru_cache


app = FastAPI()

app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_methods=["*"],
    allow_headers=["*"],
)


# ---------------------------
# LAZY LOADING OF HEAVY MODELS
# ---------------------------

@lru_cache(maxsize=1)
def get_grammar_tool():
    """Start LanguageTool server only once, on the first request."""
    return language_tool_python.LanguageTool('en-US')


@lru_cache(maxsize=1)
def get_sentiment_analyzer():
    return SentimentIntensityAnalyzer()


@lru_cache(maxsize=1)
def get_summarizer():
    return pipeline("summarization", model="facebook/bart-large-cnn")


# ---------------------------
# ANALYSIS FUNCTIONS
# ---------------------------

async def grammar_check(text):
    tool = get_grammar_tool()
    matches = tool.check(text)

    return [
        {"message": m.message, "context": m.context, "suggestions": m.replacements}
        for m in matches
    ]


async def readability_check(text):
    return {
        "flesch": textstat.flesch_reading_ease(text),
        "grade_level": textstat.flesch_kincaid_grade(text),
        "reading_time": round(textstat.reading_time(text), 2)
    }


async def tone_analysis(text):
    analyzer = get_sentiment_analyzer()
    sentiment = analyzer.polarity_scores(text)

    tone = "Positive" if sentiment['compound'] > 0.2 \
        else "Negative" if sentiment['compound'] < -0.2 \
        else "Neutral"

    return {"tone": tone, "score": sentiment['compound']}


async def engagement_check(text):
    summarizer = get_summarizer()
    summary = summarizer(text, max_length=30, min_length=5, do_sample=False)
    return {"summary": summary[0]['summary_text']}


# ---------------------------
# API ROUTE
# ---------------------------

@app.post("/analyze")
async def analyze(request: Request):
    data = await request.json()
    text = data.get("text", "")

    grammar_task = asyncio.create_task(grammar_check(text))
    read_task = asyncio.create_task(readability_check(text))
    tone_task = asyncio.create_task(tone_analysis(text))
    engage_task = asyncio.create_task(engagement_check(text))

    grammar, readability, tone, engagement = await asyncio.gather(
        grammar_task, read_task, tone_task, engage_task
    )

    return {
        "grammar": grammar,
        "readability": readability,
        "tone": tone,
        "engagement": engagement
    }
```


Your API runs at:
👉 `http://127.0.0.1:8000/analyze` 
To run it locally: We can run it locally using Uvicorn e.g  
```bash
uvicorn main:app --reload  # if your main app is named main.py  
or
uvicorn app:app --reload  # if your main app is named app.py  
```
Ensure you `cd` to the directory where the `main.py` or `app.py` is located before starting the uvicorn server  
* `main`: refers to the name of your Python file (e.g., `main.py`).
* `app`: refers to the FastAPI instance you created within that file (e.g., `app = FastAPI()`).
* `--reload`: (optional) enables automatic reloading of the server when code changes are detected, which is useful during development.  

**Verify the fastapi Server is running**
Once running, you can verify your server by checking the interactive fastapi documentation. i.e  

Open your browser and navigate to: http://127.0.0.1:8000/docs. This will confirm the API is up and ready to receive requests from your frontend  

Ensure `fastapi` and `uvicorn` are installed for the backend server to run.  

```Bash
pip install fastapi uvicorn
```
---
## You can test your endpoint to see if **Grammar, Engagement, Readability, and Tone**
are responding correctly before proceeding with frontend work. You can do it on Postman or Thunderclient.  

**We are going to do it on thunderclient**

**Step 1: Install Thunder Client**
Thunder client is a vs code `extension`. To install it:  
- Go to vscode- then extensions
- Search for thunder client and install it
- It should appear on the side of the vscode after installation, click on it to open it  

**Step 2: Create a New Request**

1. In Thunder Client, click **New Request**
2. Choose method: **POST**
3. In the URL field, e.g ours is:

```
http://127.0.0.1:8000/analyze
```

**Step 3: Add the JSON body**

1. Click the **Body** tab
2. Select **JSON**
3. Paste your test text inside:

```json
{
  "text": "This is a sample text that may contains grammar issues and also we want to check engagement and tone."
}
```

**Step 4: Hit SEND**

Click the **Send** button.

Your API will return something like:

```json
{
  "grammar": [
      {
          "message": "Possible grammar error",
          "context": "text around the issue",
          "suggestions": ["suggestion1", "suggestion2"]
      }
  ],
  "readability": {
      "flesch": 65.2,
      "grade_level": 7.3,
      "reading_time": 0.12
  },
  "tone": {
      "tone": "Neutral",
      "score": 0.05
  },
  "engagement": {
      "summary": "A short summary generated from your text."
  }
}
```
You've now tested:

* **Grammar check**
* **Engagement summary**
* **Readability score**
* **Tone sentiment**

---

## 💡 TIP: Test each part separately by changing the text

**Example: test only grammar**

```json
{
  "text": "This sentences are bad constructed."
}
```

**Test tone**

```json
{
  "text": "I am very happy with this amazing experience!"
}
```

**Test readability**

```json
{
  "text": "In an attempt to elucidate the multifaceted intricacies..."
}
```

**Test summarization (engagement)**

Paste a long paragraph.

---


## 🖥️ 4. FRONTEND (React + Bootstrap)  

### 1. Install Node.js and npm

  * **Check Installation:**
    ```bash
    node -v
    npm -v
    ```
  * If not installed, download and install the **LTS (Long Term Support) version** from the [Node.js official website](https://nodejs.org/).
  * or use terminal commandline e.g
```bash
    sudo apt install nodejs npm
```

-----

### 2. Create React App (Recommended: Use Vite)

For faster setup and development, **Vite** is the modern standard, replacing `create-react-app`.

  * **Navigate to Project Root:** e.g  
    ```bash
    cd writewise
    ```
  * **Create Vite Project:**
    ```bash
    npm create vite@latest frontend -- --template react
    ```
    (This creates the app using the official React template.)
  * **Go into Frontend Folder:**
    ```bash
    cd frontend
    ```
  * **Install Node Modules:**
    ```bash
    npm install
    ```
Vite works with node version 20 and above. If you get error installing vite then,

✅ Remove old Node
✅ Install latest Node

### **STEP 1 — Remove the old Node**

```bash
sudo apt remove nodejs npm -y
```

### Then Remove all old Node files

```bash
sudo apt remove --purge nodejs libnode-dev npm -y
sudo apt autoremove -y
```

> `--purge` ensures config files and headers are removed.
> `autoremove` cleans any leftover dependencies.

### Then Clean the apt cache

```bash
sudo apt clean
sudo rm -rf /var/cache/apt/archives/nodejs_*.deb
```

This prevents the broken `.deb` from being reused.


### **STEP 2 — Install Node 20 (or any new version you want)**

Copy/paste these:

```bash
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs
```
if you want another version, just change `_20.x` with the version you want  

### **STEP 3 — Confirm**

```bash
node -v
npm -v
```

You will now see Node 20 installed (or the new version you indicated).


# 🟢 After Node 20 is installed → Vite will work

Try again:

```bash
npm create vite@latest frontend -- --template react
```

-----

### 3. Install Dependencies and Configuration

Install core libraries, making sure you are inside the `frontend` directory.

  * **Install Bootstrap for Styling:**
    ```bash
    npm install bootstrap
    ```
  * **Import Bootstrap CSS:** Add the following line to your main entry file (usually **`src/main.jsx`** or **`src/index.js`**):
    ```javascript
    import 'bootstrap/dist/css/bootstrap.min.css';
    ```
  * **Optional: Install Axios for HTTP requests:**
    ```bash
    npm install axios
    ```
-----

### 4. Folder and Component Structure

Organize your application within **`frontend/src/`** for maintainability.

```
frontend/src/
│
├── components/
│   ├── Editor.js       # Text input area
│   ├── ResultCard.js   # Display analysis results
│   └── Navbar.js       # Top navigation bar
├── App.jsx             # Main application logic/layout
└── main.jsx            # (or index.js) Application entry point
```

-----

### 5. Connect Frontend to Backend (Environment Variables)

Create an environment variable to securely store your API URL.

  * In the **`frontend`** folder, create a **`.env`** file:
      * **If using Vite:** Variables must be prefixed with `VITE_`.
        ```
        VITE_API_URL=http://127.0.0.1:8000/analyze
        ```
      * **If using create-react-app (CRA):** Variables must be prefixed with `REACT_APP_`.
        ```
        REACT_APP_API_URL=http://127.0.0.1:8000/analyze
        ```
  * **Access in your React App:**
    ```javascript
    // If using Vite
    const API_URL = import.meta.env.VITE_API_URL; 

    // If using CRA
    const API_URL = process.env.REACT_APP_API_URL;
    ```

-----

### 6. Run React Locally

  * **Make sure your backend is running** at `http://127.0.0.1:8000` for local testing.
  * **Start the Development Server:**
    ```bash
    cd frontend
    npm run dev  # If using Vite
    # OR
    npm start    # If using create-react-app
    ```

The app will typically open at `http://localhost:5173` (Vite) or `http://localhost:3000` (CRA).

-----

### 7. Deployment Notes

  * **Update `.env`:** Replace the local URL with your **Render backend URL** before deploying.
  * **Deployment Platforms:** You can deploy the frontend to **Vercel, Netlify, or GitHub Pages.**
  * **Crucial Step: CORS:** Ensure **CORS is properly configured** and enabled in your FastAPI backend to allow the deployed frontend to communicate with the API.  

---

### `App.js`


```javascript
import React, { useState } from "react";
import "bootstrap/dist/css/bootstrap.min.css";

function App() {
  const [text, setText] = useState("");
  const [result, setResult] = useState(null);
  const [loading, setLoading] = useState(false);

  const API_URL = "https://writewise-backend.onrender.com/analyze"; // replace later

  const analyzeText = async () => {
    setLoading(true);
    try {
      const response = await fetch(API_URL, {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ text }),
      });
      const data = await response.json();
      setResult(data);
    } catch (err) {
      console.error("Error:", err);
      alert("Failed to connect to backend!");
    }
    setLoading(false);
  };

  return (
    <div className="container mt-5">
      <h2 className="text-center mb-4">✍️ WriteWise — Grammar & Readability Checker</h2>

      <textarea
        className="form-control mb-3"
        rows="8"
        placeholder="Type or paste your text..."
        value={text}
        onChange={(e) => setText(e.target.value)}
      ></textarea>

      <div className="text-center">
        <button
          className="btn btn-primary px-4"
          onClick={analyzeText}
          disabled={loading || !text.trim()}
        >
          {loading ? "Analyzing..." : "Check Text"}
        </button>
      </div>

      {result && (
        <div className="mt-4">
          <h5>🧠 Readability</h5>
          <ul>
            <li>Flesch Score: {result.readability.flesch}</li>
            <li>Grade Level: {result.readability.grade_level}</li>
            <li>Reading Time: {result.readability.reading_time} min</li>
          </ul>

          <h5>✏️ Grammar Suggestions</h5>
          {result.grammar.length === 0 ? (
            <p>No grammar issues found — excellent writing!</p>
          ) : (
            <ul>
              {result.grammar.map((c, i) => (
                <li key={i}>
                  <strong>{c.message}</strong> — <em>{c.context}</em>
                  {c.suggestions.length > 0 && (
                    <span> (Try: {c.suggestions.join(", ")})</span>
                  )}
                </li>
              ))}
            </ul>
          )}
        </div>
      )}
    </div>
  );
}

export default App;
```

---

---

## 🧠 Use Efficient Hosting Setup

| Need        | Recommendation                        | Reason                                             |
| ----------- | ------------------------------------- | -------------------------------------------------- |
| Backend     | **Render.com** or **Railway.app**     | Free, keeps small FastAPI app live with decent CPU |
| Grammar API | **LanguageTool Public API**           | Cloud-based, fast response                         |
| Frontend    | **GitHub Pages** or **Vercel**        | Free + global CDN = fast delivery                  |
| Caching     | Use a local cache (e.g., Redis later) | Prevents re-processing same text                   |


---

## ⚙️ 2. Tools and Libraries for Each Module

| Feature                    | Recommended Tools                                                                                                | Description                                              |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------- |
| **Grammar Check**          | `language_tool_python` or [LanguageTool API](https://languagetool.org/http-api/swagger-ui/#!/default/post_check) | Checks spelling, grammar, punctuation                    |
| **Readability**            | `textstat`                                                                                                       | Returns reading ease, grade level, and reading time      |
| **Accuracy & Conciseness** | `re` + small language models (OpenAI GPT, Gemini, or HuggingFace models)                                         | Detects filler words, redundancies                       |
| **Tone / Sentiment**       | `VADER`, `TextBlob`, or HuggingFace “sentiment-analysis” pipeline                                                | Detects tone (positive, neutral, negative) and formality |
| **Engagement**             | OpenAI or HuggingFace text-classification models                                                                 | Evaluates liveliness, active voice, energy level         |
| **Rewrite Suggestions**    | GPT API or `transformers` paraphrasing models                                                                    | Rewrites dull or complex text                            |
| **Plagiarism (optional)**  | Copyleaks / Grammarly API / Turnitin API                                                                         | Checks originality of text                               |

---

---

# 6) Concrete backend implementation sketches

### `backend/app.py` (entry)

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from api.analyze import analyze_router

app = FastAPI()
app.add_middleware(CORSMiddleware, allow_origins=["*"], allow_methods=["*"], allow_headers=["*"])

app.include_router(analyze_router, prefix="/api")
```

### `backend/api/analyze.py` (orchestrator)

```python
from fastapi import APIRouter, Request, HTTPException
import asyncio, time, hashlib, json
from services.cache import cache_get, cache_set
from api.modules import grammar, readability, tone, engagement, accuracy, rewrite

analyze_router = APIRouter()

def make_key(text, options):
    h = hashlib.sha256((text + json.dumps(options, sort_keys=True)).encode()).hexdigest()
    return f"analysis:{h}"

@analyze_router.post("/analyze")
async def analyze(request: Request):
    payload = await request.json()
    text = payload.get("text", "")
    options = payload.get("options", {})

    if not text or len(text.strip()) == 0:
        raise HTTPException(status_code=400, detail="Text is empty")

    key = make_key(text, options)
    cached = cache_get(key)
    if cached:
        return cached

    start = time.time()
    # create tasks (run concurrently)
    tasks = [
        asyncio.create_task(grammar.check(text)),
        asyncio.create_task(readability.check(text)),
        asyncio.create_task(tone.analyze(text)),
        asyncio.create_task(engagement.score(text)),
        asyncio.create_task(accuracy.scan(text))
    ]
    if options.get("rewrite"):
        tasks.append(asyncio.create_task(rewrite.generate(text, options.get("tone"))))

    # set an overall timeout (e.g. 10s)
    try:
        results = await asyncio.wait_for(asyncio.gather(*tasks), timeout=12.0)
    except asyncio.TimeoutError:
        # gather what finished, return partial results with timeout flag
        # (left as exercise to implement per-task inspection)
        results = []

    processing_ms = int((time.time() - start) * 1000)
    response = {
        "meta": {"processing_ms": processing_ms},
        "grammar": results[0] if len(results) > 0 else {},
        "readability": results[1] if len(results) > 1 else {},
        "tone": results[2] if len(results) > 2 else {},
        "engagement": results[3] if len(results) > 3 else {},
        "accuracy": results[4] if len(results) > 4 else {},
        "rewrite": results[5] if len(results) > 5 else {}
    }

    cache_set(key, response, ttl=300)  # cache 5 minutes
    return response
```

### Example module: `backend/api/modules/grammar.py`

```python
import language_tool_python
tool = language_tool_python.LanguageTool('en-US')

async def check(text):
    # language_tool_python is sync; run in thread
    from asyncio import to_thread
    matches = await to_thread(tool.check, text)
    issues = []
    for m in matches:
        issues.append({
            "message": m.message,
            "offset": m.offset,
            "length": m.errorLength,
            "context": m.context,
            "replacements": m.replacements
        })
    summary = {"count": len(issues)}
    return {"issues": issues, "summary": summary}
```

### Example module: `backend/api/modules/readability.py`

```python
import textstat
from asyncio import to_thread

async def check(text):
    # textstat is sync; use thread
    fq = await to_thread(textstat.flesch_reading_ease, text)
    grade = await to_thread(textstat.flesch_kincaid_grade, text)
    time_min = await to_thread(textstat.reading_time, text)
    return {"flesch": fq, "grade_level": grade, "reading_time_min": round(time_min, 2)}
```

### Example module: `backend/api/modules/tone.py`

```python
from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer
analyzer = SentimentIntensityAnalyzer()

async def analyze(text):
    from asyncio import to_thread
    scores = await to_thread(analyzer.polarity_scores, text)
    compound = scores["compound"]
    label = "Positive" if compound > 0.2 else "Negative" if compound < -0.2 else "Neutral"
    return {"label": label, "scores": scores}
```

### Rewrite (LLM) module example: `backend/api/modules/rewrite.py`

```python
# Example using OpenAI (replace with HF or local model if you prefer)
import os
import openai
openai.api_key = os.getenv("OPENAI_API_KEY")

async def generate(text, tone=None):
    # simple prompt example
    prompt = f"Rewrite the following text to be {tone or 'clear and engaging'}. Keep meaning intact.\n\nText:\n{text}\n\nRewrite:"
    resp = await openai.ChatCompletion.acreate(
        model="gpt-4o-mini", # swap model; ensure you have access
        messages=[{"role":"user","content":prompt}],
        max_tokens=400
    )
    out = resp.choices[0].message.content.strip()
    return {"paraphrases": [{"label": tone or "improved", "text": out}]}
```

> **Note:** handle tokens, truncation, and fallbacks. If you don’t have OpenAI, use HuggingFace inference API or a local paraphrase model.

---

# 7) Caching & performance

* Use Redis for production. Simple function:

  * key = SHA256(text + options)
  * TTL 5–30 minutes depending on your use case
* For grammar modules that are deterministic, caching pays big returns.
* For LLM rewrites, cache responses to save API spend.

---

# 8) Frontend integration (React + Bootstrap)

* Editor area with large textarea.
* Submit button → `POST /api/analyze`.
* Show spinner and incremental result placeholders.
* Render modules as cards (Grammar, Readability, Tone, Engagement, Accuracy, Rewrites).
* For grammar suggestions, implement **text highlighting**. Provide an “Apply suggestion” button that replaces text in the editor.
* Use `localStorage` to persist last draft and last result.
* Debounce auto-check: 1500–2500ms idle before auto-scan.

Example fetch helper `frontend/src/api.js`:

```javascript
export async function analyze(text, options={}) {
  const res = await fetch("/api/analyze", {
    method: "POST", headers: {"Content-Type":"application/json"},
    body: JSON.stringify({text, options})
  });
  return await res.json();
}
```

---

# 9) UX considerations & features

* **Instant visual feedback**: spinner, animated progress bar.
* **Partial updates**: render readability & tone first, grammar when ready, rewrite last.
* **Inline suggestions**: hover grammar highlights to show suggestion list.
* **One-click apply**: accept suggestion toggles editor text.
* **Modes**: “Draft”, “Formal”, “Casual”, “Academic” — map to different rewrite prompts.
* **Explain suggestions**: short reason (“passive voice detected — consider active voice”).
* **Export**: PDF or copy optimized text.

---

# 10) Prompts and templates (for reliable rewrites & scoring)

Use carefully-crafted prompts. Examples:

Rewrite prompt (concise/formal):

```
Rewrite the following paragraph to be concise and formal without changing meaning. Preserve technical terms.

Paragraph:
{TEXT}

Rewrite:
```

Engagement scoring prompt:

```
On a scale 0-1, how engaging is the following text? Consider hooks, sentence variety, clarity, and active voice. Return JSON: {"score":0.0,"reason":"short reason"}.

Text:
{TEXT}
```

Tone classification prompt:

```
Classify the text into one of: Formal, Informal, Neutral, Friendly, Persuasive, Academic. Return JSON: {"label":"Formal","confidence":0.XX}.

Text:
{TEXT}
```

When using LLMs, require JSON-only responses and validate/parse them server-side.

---

## Recommended backend codes:
**optimized code version: 1**  
```python
# main.py
import asyncio
from concurrent.futures import ThreadPoolExecutor
from typing import Any, Dict, List

from fastapi import FastAPI, HTTPException
from fastapi.middleware.cors import CORSMiddleware
from pydantic import BaseModel, Field
import textstat
from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer
import language_tool_python
from transformers import pipeline
import logging
import os

# --------- Configuration ----------
MAX_INPUT_CHARS = 20000  # adjust to your limits
EXECUTOR_WORKERS = int(os.getenv("EXECUTOR_WORKERS", "4"))
ALLOWED_ORIGINS = [
    "http://localhost:3000",
    "http://localhost:5173",
    # "https://your-deployed-frontend-url.com"  <-- Replace with your frontend URL in production
]

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger("ai-writing-assistant")

# --------- Thread pool for blocking CPU work ----------
executor = ThreadPoolExecutor(max_workers=EXECUTOR_WORKERS)

# --------- FastAPI app ----------
app = FastAPI(title="AI Writing Assistant API")

app.add_middleware(
    CORSMiddleware,
    allow_origins=ALLOWED_ORIGINS,
    allow_credentials=True,
    allow_methods=["GET", "POST", "OPTIONS"],
    allow_headers=["*"],
)

# --------- Load heavy models once (startup) ----------
# Note: model downloads happen the first time you instantiate pipeline; ensure network availability
logger.info("Loading models... (this may take a moment on first run)")
tool = language_tool_python.LanguageTool("en-US")
sentiment_analyzer = SentimentIntensityAnalyzer()

# Lightweight summarizer - change model if you have resource constraints
summarizer = pipeline("summarization", model="sshleifer/distilbart-cnn-6-6")
logger.info("Models loaded.")


# --------- Request / Response models ----------
class TextRequest(BaseModel):
    text: str = Field(..., description="Text to analyze", min_length=1, max_length=MAX_INPUT_CHARS)


class CorrectionItem(BaseModel):
    message: str
    context: str
    suggestions: List[str]


# --------- Helper sync functions (will be executed in threadpool) ----------
def _grammar_sync(text: str) -> List[Dict[str, Any]]:
    matches = tool.check(text)
    return [
        {
            "message": m.message,
            "context": getattr(m, "context", ""),
            "suggestions": list(m.replacements) if m.replacements else [],
        }
        for m in matches
    ]


def _summarize_sync(text: str, max_length: int = 60, min_length: int = 10) -> str:
    # summarizer returns a list of dicts with 'summary_text'
    res = summarizer(text, max_length=max_length, min_length=min_length, do_sample=False)
    return res[0]["summary_text"] if res and isinstance(res, list) else ""


# --------- Async wrappers that offload blocking calls ----------
async def grammar_check(text: str) -> List[Dict[str, Any]]:
    loop = asyncio.get_event_loop()
    return await loop.run_in_executor(executor, _grammar_sync, text)


async def readability_check(text: str) -> Dict[str, Any]:
    # textstat is relatively light, but keep it async-compatible
    return {
        "flesch_reading_ease": textstat.flesch_reading_ease(text),
        "grade_level": textstat.flesch_kincaid_grade(text),
        "reading_time_minutes": round(textstat.reading_time(text), 2),
    }


async def tone_analysis(text: str) -> Dict[str, Any]:
    # VADER is fast; run inline
    sentiment = sentiment_analyzer.polarity_scores(text)
    score = sentiment.get("compound", 0.0)
    tone = "Positive" if score > 0.2 else "Negative" if score < -0.2 else "Neutral"
    return {"tone": tone, "score": score}


async def engagement_check(text: str) -> Dict[str, Any]:
    # Summarizer is blocking — run in executor
    loop = asyncio.get_event_loop()
    summary = await loop.run_in_executor(executor, _summarize_sync, text, 60, 10)
    return {"summary": summary}


# --------- API endpoints ----------
@app.post("/analyze")
async def analyze_text(payload: TextRequest):
    text = payload.text.strip()

    if not text:
        raise HTTPException(status_code=400, detail="Text cannot be empty.")

    if len(text) > MAX_INPUT_CHARS:
        raise HTTPException(status_code=413, detail=f"Text too long. Max {MAX_INPUT_CHARS} characters allowed.")

    try:
        # Run tasks concurrently
        grammar_task = asyncio.create_task(grammar_check(text))
        readability_task = asyncio.create_task(readability_check(text))
        tone_task = asyncio.create_task(tone_analysis(text))
        engagement_task = asyncio.create_task(engagement_check(text))

        corrections, readability, tone, engagement = await asyncio.gather(
            grammar_task, readability_task, tone_task, engagement_task
        )

        return {
            "corrections": corrections,       # frontend-friendly key (matches your frontend expectation)
            "readability": readability,
            "tone": tone,
            "engagement": engagement,
        }

    except Exception as exc:
        logger.exception("Error during analysis")
        raise HTTPException(status_code=500, detail=str(exc))


@app.get("/health")
def health_check():
    return {"status": "ok"}


# --------- Graceful shutdown ----------
@app.on_event("shutdown")
def shutdown_event():
    logger.info("Shutting down executor...")
    executor.shutdown(wait=True)
    logger.info("Executor shut down.")

```
---

**Optimized code version: 2**
```python
# optimized_main.py
import asyncio
from concurrent.futures import ThreadPoolExecutor
from typing import Any, Dict, List
import os
import logging

from fastapi import FastAPI, HTTPException
from fastapi.middleware.cors import CORSMiddleware
from pydantic import BaseModel, Field
import textstat
from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer
import language_tool_python
from transformers import pipeline

# -------- Logging ----------
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger("ai-writing-assistant")

# -------- Config ----------
MAX_INPUT_CHARS = 50000
EXECUTOR_WORKERS = int(os.getenv("EXECUTOR_WORKERS", "4"))
ALLOWED_ORIGINS = [
    "http://localhost:3000",
    "http://localhost:5173",
]

# -------- ThreadPool ----------
executor = ThreadPoolExecutor(max_workers=EXECUTOR_WORKERS)

# -------- FastAPI ----------
app = FastAPI(title="AI Writing Assistant API")

app.add_middleware(
    CORSMiddleware,
    allow_origins=ALLOWED_ORIGINS,
    allow_credentials=True,
    allow_methods=["GET", "POST", "OPTIONS"],
    allow_headers=["*"],
)

# -------- Lazy model loading ----------
def get_grammar_tool():
    if not hasattr(get_grammar_tool, "instance"):
        logger.info("Starting LanguageTool server...")
        get_grammar_tool.instance = language_tool_python.LanguageTool("en-US")
        logger.info("LanguageTool ready.")
    return get_grammar_tool.instance


def get_sentiment_analyzer():
    if not hasattr(get_sentiment_analyzer, "instance"):
        get_sentiment_analyzer.instance = SentimentIntensityAnalyzer()
    return get_sentiment_analyzer.instance


def get_summarizer():
    if not hasattr(get_summarizer, "instance"):
        device = 0 if os.environ.get("USE_GPU", "0") == "1" else -1
        logger.info(f"Loading summarizer model on device {device}...")
        get_summarizer.instance = pipeline(
            "summarization",
            model="sshleifer/distilbart-cnn-6-6",
            device=device
        )
        logger.info("Summarizer ready.")
    return get_summarizer.instance


# -------- Request / Response ----------
class TextRequest(BaseModel):
    text: str = Field(..., min_length=1, max_length=MAX_INPUT_CHARS)


# -------- Helper sync functions ----------
def _grammar_sync(text: str) -> List[Dict[str, Any]]:
    tool = get_grammar_tool()
    matches = tool.check(text)
    return [
        {
            "message": m.message,
            "context": getattr(m, "context", ""),
            "suggestions": list(m.replacements) if m.replacements else [],
        }
        for m in matches
    ]


def _summarize_sync(text: str) -> str:
    summarizer = get_summarizer()
    res = summarizer(text, do_sample=False)
    return res[0]["summary_text"] if res else ""  


# -------- Async wrappers ----------
async def grammar_check(text: str):
    loop = asyncio.get_running_loop()
    return await loop.run_in_executor(executor, _grammar_sync, text)


async def readability_check(text: str):
    return {
        "flesch_reading_ease": textstat.flesch_reading_ease(text),
        "grade_level": textstat.flesch_kincaid_grade(text),
        "reading_time_minutes": round(textstat.reading_time(text), 2),
    }


async def tone_analysis(text: str):
    analyzer = get_sentiment_analyzer()
    sentiment = analyzer.polarity_scores(text)
    score = sentiment.get("compound", 0.0)
    tone = "Positive" if score > 0.2 else "Negative" if score < -0.2 else "Neutral"
    return {"tone": tone, "score": score}


async def engagement_check(text: str):
    loop = asyncio.get_running_loop()
    summary = await loop.run_in_executor(executor, _summarize_sync, text)
    return {"summary": summary}


# -------- API endpoint ----------
@app.post("/analyze")
async def analyze_text(payload: TextRequest):
    text = payload.text.strip()
    if not text:
        raise HTTPException(status_code=400, detail="Text cannot be empty.")

    try:
        tasks = [
            asyncio.create_task(grammar_check(text)),
            asyncio.create_task(readability_check(text)),
            asyncio.create_task(tone_analysis(text)),
            asyncio.create_task(engagement_check(text)),
        ]
        grammar, readability, tone, engagement = await asyncio.gather(*tasks)
        return {
            "corrections": grammar,
            "readability": readability,
            "tone": tone,
            "engagement": engagement,
        }
    except Exception as e:
        logger.exception("Error during analysis")
        raise HTTPException(status_code=500, detail=str(e))


@app.get("/health")
def health_check():
    return {"status": "ok"}


# -------- Graceful shutdown ----------
@app.on_event("shutdown")
def shutdown_event():
    logger.info("Shutting down executor...")
    executor.shutdown(wait=True)
    logger.info("Executor shut down.")
```
---
**Optimized code version: 3**
```bash
# app_main.py
import asyncio
import logging
import os
import re
from concurrent.futures import ThreadPoolExecutor
from typing import Any, Dict, List, Optional, Tuple

from fastapi import FastAPI, HTTPException, Request
from fastapi.middleware.cors import CORSMiddleware
from pydantic import BaseModel, Field
from pydantic_settings import BaseSettings
from starlette.concurrency import run_in_threadpool

# third-party libs (same as original)
import textstat
from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer
import language_tool_python
from transformers import pipeline, Pipeline

# Optional spell libraries
try:
    from symspellpy import SymSpell, Verbosity  # type: ignore
    SYMSPELL_AVAILABLE = True
except Exception:
    SYMSPELL_AVAILABLE = False

try:
    from spellchecker import SpellChecker  # type: ignore
    PYSPELL_AVAILABLE = True
except Exception:
    PYSPELL_AVAILABLE = False

logger = logging.getLogger("ai-writing-assistant")
logging.basicConfig(level=logging.INFO)


# ---------------- Configuration ----------------
class Settings(BaseSettings):
    max_input_chars: int = 50_000
    executor_workers: int = int(os.getenv("EXECUTOR_WORKERS", "4"))
    allowed_origins: List[str] = ["http://localhost:3000", "http://localhost:5173"]
    use_gpu: bool = os.getenv("USE_GPU", "0") == "1"
    summarizer_model: str = os.getenv("SUMMARIZER_MODEL", "facebook/bart-large-cnn")
    short_text_word_threshold: int = int(os.getenv("SHORT_TEXT_WORD_THRESHOLD", "20"))
    min_sentence_for_readability: int = int(os.getenv("MIN_SENTENCE_FOR_READABILITY", "3"))
    symspell_freq_path: Optional[str] = os.getenv("SYMSPELL_FREQ_PATH")  # optional path for symspell dict
    request_timeout_seconds: int = int(os.getenv("REQUEST_TIMEOUT_SECONDS", "30"))

    class Config:
        env_file = ".env"


settings = Settings()


# ---------------- FastAPI setup ----------------
app = FastAPI(title="AI Writing Assistant API")

app.add_middleware(
    CORSMiddleware,
    allow_origins=settings.allowed_origins,
    allow_credentials=True,
    allow_methods=["GET", "POST", "OPTIONS"],
    allow_headers=["*"],
)


# ---------------- models / types ----------------
class TextRequest(BaseModel):
    text: str = Field(..., min_length=1, max_length=settings.max_input_chars)


# ---------------- Utility helpers ----------------
_TOKEN_RE = re.compile(r"\S+")


def tokenize_preserving_indices(text: str) -> List[Tuple[str, int, int]]:
    return [(m.group(0), m.start(), m.end()) for m in _TOKEN_RE.finditer(text)]


def simple_normalize_text(text: str) -> str:
    text = text.strip()
    text = re.sub(r"\s+", " ", text)
    text = re.sub(r"\s+([,.;:!?])", r"\1", text)
    return text


# ---------------- App state initialization ----------------
@app.on_event("startup")
async def startup_event():
    # Create executor and store on app.state so we can gracefully shutdown.
    app.state.executor = ThreadPoolExecutor(max_workers=settings.executor_workers)
    # Async locks for lazy init
    app.state._model_locks = {
        "grammar": asyncio.Lock(),
        "sentiment": asyncio.Lock(),
        "summarizer": asyncio.Lock(),
        "spell": asyncio.Lock(),
    }
    # Instances (populated lazily)
    app.state._instances = {
        "grammar": None,
        "sentiment": None,
        "summarizer": None,
        "spell": None,
    }
    # Concurrency guard for CPU/memory heavy ops (tune as needed)
    app.state.semaphore = asyncio.Semaphore(settings.executor_workers)
    logger.info("Startup complete. Executor and locks initialized.")


@app.on_event("shutdown")
def shutdown_event():
    executor: ThreadPoolExecutor = app.state.executor
    logger.info("Shutting down executor...")
    executor.shutdown(wait=True)
    logger.info("Executor shut down.")


# ---------------- Lazy getters with locks ----------------
async def get_grammar_tool():
    if app.state._instances["grammar"]:
        return app.state._instances["grammar"]
    async with app.state._model_locks["grammar"]:
        if app.state._instances["grammar"]:
            return app.state._instances["grammar"]
        logger.info("Initializing LanguageTool...")
        # language_tool_python runs a Java backend; it is sync so we create in threadpool
        def init():
            return language_tool_python.LanguageTool("en-US")
        tool = await run_in_threadpool(init)
        app.state._instances["grammar"] = tool
        logger.info("LanguageTool ready.")
        return tool


async def get_sentiment_analyzer():
    if app.state._instances["sentiment"]:
        return app.state._instances["sentiment"]
    async with app.state._model_locks["sentiment"]:
        if app.state._instances["sentiment"]:
            return app.state._instances["sentiment"]
        logger.info("Initializing VADER sentiment analyzer...")
        app.state._instances["sentiment"] = SentimentIntensityAnalyzer()
        return app.state._instances["sentiment"]


async def get_summarizer() -> Optional[Pipeline]:
    if app.state._instances["summarizer"]:
        return app.state._instances["summarizer"]
    async with app.state._model_locks["summarizer"]:
        if app.state._instances["summarizer"]:
            return app.state._instances["summarizer"]
        device = 0 if settings.use_gpu else -1
        try:
            logger.info(f"Loading summarizer model {settings.summarizer_model} on device {device}...")
            # Keep as possibly heavy; creation done in threadpool
            def init():
                return pipeline("summarization", model=settings.summarizer_model, device=device)
            summarizer = await run_in_threadpool(init)
            app.state._instances["summarizer"] = summarizer
            logger.info("Summarizer ready.")
        except Exception as e:
            logger.exception("Failed to load summarizer model; continuing without it.")
            app.state._instances["summarizer"] = None
        return app.state._instances["summarizer"]


async def get_spell_checker():
    if app.state._instances["spell"]:
        return app.state._instances["spell"]
    async with app.state._model_locks["spell"]:
        if app.state._instances["spell"]:
            return app.state._instances["spell"]
        # Prefer symspell if available and dictionary path provided
        if SYMSPELL_AVAILABLE:
            try:
                def init_symspell():
                    sym = SymSpell(max_dictionary_edit_distance=2, prefix_length=7)
                    if settings.symspell_freq_path:
                        sym.load_dictionary(settings.symspell_freq_path, term_index=0, count_index=1)
                    return ("symspell", sym)
                spell_inst = await run_in_threadpool(init_symspell)
                logger.info("Using SymSpell for spelling.")
                app.state._instances["spell"] = spell_inst
                return spell_inst
            except Exception:
                logger.exception("SymSpell init failed; falling back...")
        if PYSPELL_AVAILABLE:
            logger.info("Using pyspellchecker for spelling.")
            sp = SpellChecker(language="en")
            app.state._instances["spell"] = ("pyspell", sp)
            return ("pyspell", sp)
        logger.warning("No spellchecker available.")
        app.state._instances["spell"] = ("none", None)
        return ("none", None)


# ---------------- Core synchronous functions (executed in threadpool) ----------------
def _spell_corrections_sync(text: str, spell_inst) -> Dict[str, Any]:
    checker_type, checker = spell_inst
    tokens = tokenize_preserving_indices(text)
    corrections = []
    corrected_text = text
    offset_adjust = 0

    if checker_type == "symspell":
        sym: SymSpell = checker  # type: ignore
        for token, start, end in tokens:
            if re.fullmatch(r"^[\W_]+$", token) or re.fullmatch(r"^\d+$", token):
                continue
            suggestions = []
            try:
                suggs = sym.lookup(token, Verbosity.CLOSEST, max_edit_distance=2)
                suggestions = [s.term for s in suggs]
            except Exception:
                suggestions = []
            if suggestions and suggestions[0].lower() != token.lower():
                best = suggestions[0]
                real_start = start + offset_adjust
                real_end = end + offset_adjust
                corrected_text = corrected_text[:real_start] + best + corrected_text[real_end:]
                offset_adjust += len(best) - (end - start)
                corrections.append({
                    "token": token,
                    "start": real_start,
                    "end": real_start + len(best),
                    "suggestions": suggestions,
                    "best": best,
                })
    elif checker_type == "pyspell":
        sp = checker  # type: ignore
        for token, start, end in tokens:
            if re.fullmatch(r"^[\W_]+$", token) or re.fullmatch(r"^\d+$", token):
                continue
            raw = re.sub(r"^[^A-Za-z0-9']+|[^A-Za-z0-9']+$", "", token)
            if not raw:
                continue
            if raw.lower() in sp:
                continue
            candidates = list(sp.candidates(raw))
            suggestions = sorted(candidates, key=lambda c: sp.word_probability(c), reverse=True) if candidates else []
            best = suggestions[0] if suggestions else None
            if best and best.lower() != raw.lower():
                real_start = start + offset_adjust
                real_end = end + offset_adjust
                best_cased = best.capitalize() if raw[0].isupper() else best
                corrected_text = corrected_text[:real_start] + best_cased + corrected_text[real_end:]
                offset_adjust += len(best_cased) - (end - start)
                corrections.append({
                    "token": token,
                    "start": real_start,
                    "end": real_start + len(best_cased),
                    "suggestions": suggestions,
                    "best": best_cased,
                })
    else:
        return {"corrected_text": text, "corrections": []}
    return {"corrected_text": corrected_text, "corrections": corrections}


def _grammar_sync(text: str, tool) -> List[Dict[str, Any]]:
    matches = tool.check(text)
    results = []
    for m in matches:
        start = getattr(m, "offset", None)
        length = getattr(m, "errorLength", None) or getattr(m, "length", None)
        if start is not None and length is not None:
            start = int(start)
            length = int(length)
            end = start + length
            context = text[max(0, start - 30): min(len(text), end + 30)]
        else:
            context = (getattr(m, "context", "") or getattr(m, "message", ""))[:120]
        replacements = list(getattr(m, "replacements", [])) if getattr(m, "replacements", None) else []
        results.append({
            "message": m.message,
            "rule_id": getattr(m, "ruleId", None),
            "context": context,
            "offset": start,
            "length": length,
            "suggestions": replacements,
        })
    return results


def _summarize_sync(text: str, summarizer, short_threshold: int) -> str:
    normalized = simple_normalize_text(text)
    word_count = len(re.findall(r"\w+", normalized))
    if word_count <= short_threshold or summarizer is None:
        clarity = re.sub(r"(\b\w+\b)(?:\s+\1\b)+", r"\1", normalized, flags=re.IGNORECASE)
        return clarity if len(clarity) < 400 else clarity[:400].rsplit(" ", 1)[0] + "..."
    try:
        res = summarizer(normalized, do_sample=False, truncation=True)
        if isinstance(res, list) and res:
            return res[0].get("summary_text", normalized[:400])
        return normalized[:400]
    except Exception:
        logger.exception("Summarizer failed.")
        return normalized[:400]


def _readability_sync(text: str, min_sentences: int):
    sentences = max(1, len(re.findall(r"[.!?]+", text)))
    if sentences < min_sentences or len(text.split()) < 5:
        return {
            "flesch_reading_ease": None,
            "grade_level": None,
            "reading_time_minutes": round(textstat.reading_time(text), 2) if hasattr(textstat, "reading_time") else None,
            "note": "Not enough content to compute reliable readability scores."
        }
    return {
        "flesch_reading_ease": textstat.flesch_reading_ease(text),
        "grade_level": textstat.flesch_kincaid_grade(text),
        "reading_time_minutes": round(textstat.reading_time(text), 2),
    }


# ---------------- Async wrappers ----------------
async def spell_corrections(text: str):
    spell_inst = await get_spell_checker()
    # run heavy work in threadpool
    return await run_in_threadpool(_spell_corrections_sync, text, spell_inst)


async def grammar_check(text: str):
    tool = await get_grammar_tool()
    return await run_in_threadpool(_grammar_sync, text, tool)


async def summarize_text(text: str):
    summarizer = await get_summarizer()
    return await run_in_threadpool(_summarize_sync, text, summarizer, settings.short_text_word_threshold)


async def readability_check(text: str):
    return await run_in_threadpool(_readability_sync, text, settings.min_sentence_for_readability)


async def tone_analysis(text: str):
    analyzer = await get_sentiment_analyzer()
    words = re.findall(r"\w+", text)
    if len(words) < 3:
        return {"tone": None, "score": None, "note": "Not enough text to determine tone reliably."}
    sentiment = analyzer.polarity_scores(text)
    score = sentiment.get("compound", 0.0)
    tone = "Positive" if score > 0.2 else "Negative" if score < -0.2 else "Neutral"
    return {"tone": tone, "score": score}


# ---------------- API endpoint ----------------
@app.post("/analyze")
async def analyze_text(payload: TextRequest, request: Request):
    raw_text = payload.text or ""
    text = raw_text.strip()
    if not text:
        raise HTTPException(status_code=400, detail="Text cannot be empty.")

    # acquire concurrency guard per-request
    try:
        await asyncio.wait_for(app.state.semaphore.acquire(), timeout=settings.request_timeout_seconds)
    except asyncio.TimeoutError:
        raise HTTPException(status_code=503, detail="Server busy; try again later.")

    try:
        # 1) Spell corrections first (sync in threadpool)
        spell_result = await spell_corrections(text)
        corrected_text = spell_result.get("corrected_text", text)
        spell_items = spell_result.get("corrections", [])

        # 2) run grammar/readability/tone/summary concurrently
        coro_tasks = [
            asyncio.create_task(grammar_check(corrected_text)),
            asyncio.create_task(readability_check(corrected_text)),
            asyncio.create_task(tone_analysis(corrected_text)),
            asyncio.create_task(summarize_text(corrected_text)),
        ]
        grammar, readability, tone, engagement = await asyncio.gather(*coro_tasks)

        # 3) Merge suggestions (kept intentionally similar to original but clearer)
        merged = []
        for g in grammar:
            g_start = g.get("offset")
            g_len = g.get("length")
            g_end = (g_start + g_len) if (g_start is not None and g_len is not None) else None
            overlapping_spells = [
                s for s in spell_items
                if s.get("start") is not None and g_start is not None and g_end is not None
                and not (s["end"] <= g_start or s["start"] >= g_end)
            ]
            if overlapping_spells:
                combined = []
                for s in overlapping_spells:
                    if s.get("best"):
                        combined.append(s["best"])
                    combined.extend(s.get("suggestions", [])[:3])
                combined.extend(g.get("suggestions", [])[:3])
                merged.append({
                    "message": g.get("message"),
                    "context": g.get("context"),
                    "suggestions": combined,
                    "rule_id": g.get("rule_id"),
                    "offset": g.get("offset"),
                    "length": g.get("length"),
                })
            else:
                merged.append({
                    "message": g.get("message"),
                    "context": g.get("context"),
                    "suggestions": g.get("suggestions", []),
                    "rule_id": g.get("rule_id"),
                    "offset": g.get("offset"),
                    "length": g.get("length"),
                })

        non_overlapping_spells = []
        for s in spell_items:
            overlapped = False
            for g in grammar:
                g_start = g.get("offset")
                g_len = g.get("length")
                if g_start is None or g_len is None:
                    continue
                g_end = g_start + g_len
                if s.get("start") is not None and not (s["end"] <= g_start or s["start"] >= g_end):
                    overlapped = True
                    break
            if not overlapped:
                non_overlapping_spells.append({
                    "message": f"Possible spelling mistake: {s.get('token')}",
                    "context": corrected_text[max(0, s.get("start", 0) - 30): s.get("end", 0) + 30],
                    "suggestions": s.get("suggestions", [])[:5],
                    "best": s.get("best"),
                    "offset": s.get("start"),
                    "length": (s.get("end") - s.get("start")) if (s.get("end") is not None and s.get("start") is not None) else None,
                })

        final_corrections = merged + non_overlapping_spells

        return {
            "original_text": raw_text,
            "corrected_text": corrected_text,
            "corrections": final_corrections,
            "readability": readability,
            "tone": tone,
            "engagement": {"summary": engagement},
        }

    except Exception as e:
        logger.exception("Error during analysis pipeline")
        raise HTTPException(status_code=500, detail="Internal processing error.")
    finally:
        app.state.semaphore.release()


@app.get("/health")
async def health_check():
    return {"status": "ok"}
```
---
**Optimized code version: 4**
Real-world architecture (API → cloud inference → merge pipeline)- This id the method for industy standards  
```bash
import asyncio
import hashlib
import logging
import os
import re
from functools import lru_cache
from typing import Any, Dict, List, Optional, Tuple

from fastapi import FastAPI, HTTPException, Request, BackgroundTasks
from fastapi.middleware.cors import CORSMiddleware
from pydantic import BaseModel, Field, validator
from pydantic_settings import BaseSettings
from starlette.concurrency import run_in_threadpool
import httpx

# Third-party libs
import textstat
from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer
import language_tool_python

# Optional spell libraries
try:
    from symspellpy import SymSpell, Verbosity
    SYMSPELL_AVAILABLE = True
except ImportError:
    SYMSPELL_AVAILABLE = False

try:
    from spellchecker import SpellChecker
    PYSPELL_AVAILABLE = True
except ImportError:
    PYSPELL_AVAILABLE = False

# Optional caching
try:
    from cachetools import TTLCache
    CACHE_AVAILABLE = True
except ImportError:
    CACHE_AVAILABLE = False

logger = logging.getLogger("ai-writing-assistant")
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s'
)


# ============================================================
# Configuration
# ============================================================
class Settings(BaseSettings):
    # Input validation
    max_input_chars: int = 50_000
    min_input_chars: int = 1
    
    # Concurrency settings
    max_concurrent_requests: int = 50
    max_workers_per_service: int = 4
    
    # CORS
    allowed_origins: List[str] = ["http://localhost:3000", "http://localhost:5173"]
    
    # Remote inference
    remote_summarizer_url: str = os.getenv(
        """Add link to your inference model (e.g HuggingFace or Open AI)"""
        "REMOTE_SUMMARIZER_URL",
        "https://api-inference.huggingface.co/models/facebook/bart-large-cnn"
    )
    """add your inference token (e.g "REMOTE_SUMMARIZER_TOKEN=076-456j-64n")"""
    remote_summarizer_token: Optional[str] = os.getenv("REMOTE_SUMMARIZER_TOKEN=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")
    remote_timeout: int = 30
    
    # Analysis thresholds
    short_text_word_threshold: int = 20
    min_sentence_for_readability: int = 3
    min_words_for_tone: int = 5
    
    # Spell checker
    symspell_freq_path: Optional[str] = os.getenv("SYMSPELL_FREQ_PATH")
    spell_max_edit_distance: int = 2
    
    # Caching
    enable_cache: bool = True
    cache_ttl: int = 3600  # 1 hour
    cache_maxsize: int = 1000
    
    # Timeouts
    request_timeout: int = 60
    grammar_check_timeout: int = 30
    
    class Config:
        env_file = ".env"


settings = Settings()


# ============================================================
# FastAPI Setup
# ============================================================
app = FastAPI(
    title="AI Writing Assistant API",
    version="2.0.0",
    description="Advanced grammar, spell-checking, and text analysis API"
)

app.add_middleware(
    CORSMiddleware,
    allow_origins=settings.allowed_origins,
    allow_credentials=True,
    allow_methods=["GET", "POST", "OPTIONS"],
    allow_headers=["*"],
)


# ============================================================
# Request/Response Models
# ============================================================
class TextRequest(BaseModel):
    text: str = Field(..., min_length=settings.min_input_chars, max_length=settings.max_input_chars)
    include_grammar: bool = True
    include_spell: bool = True
    include_readability: bool = True
    include_tone: bool = True
    include_summary: bool = True
    
    @validator('text')
    def validate_text(cls, v):
        if not v or not v.strip():
            raise ValueError("Text cannot be empty or whitespace only")
        return v


class CorrectionItem(BaseModel):
    message: str
    context: str
    suggestions: List[str]
    offset: Optional[int] = None
    length: Optional[int] = None
    rule_id: Optional[str] = None
    severity: Optional[str] = "suggestion"


class AnalysisResponse(BaseModel):
    original_text: str
    corrected_text: str
    corrections: List[CorrectionItem]
    readability: Optional[Dict[str, Any]] = None
    tone: Optional[Dict[str, Any]] = None
    engagement: Optional[Dict[str, Any]] = None
    metadata: Dict[str, Any] = {}


# ============================================================
# Utility Functions
# ============================================================
_TOKEN_RE = re.compile(r"\S+")


def tokenize_preserving_indices(text: str) -> List[Tuple[str, int, int]]:
    """Tokenize text while preserving character positions."""
    return [(m.group(0), m.start(), m.end()) for m in _TOKEN_RE.finditer(text)]


def normalize_text(text: str) -> str:
    """Normalize whitespace and punctuation."""
    text = text.strip()
    text = re.sub(r'\s+', ' ', text)
    text = re.sub(r'\s+([,.;:!?])', r'\1', text)
    return text


def compute_text_hash(text: str) -> str:
    """Compute SHA256 hash of text for caching."""
    return hashlib.sha256(text.encode('utf-8')).hexdigest()


@lru_cache(maxsize=100)
def count_words(text: str) -> int:
    """Cached word counting."""
    return len(re.findall(r'\w+', text))


@lru_cache(maxsize=100)
def count_sentences(text: str) -> int:
    """Cached sentence counting."""
    return textstat.sentence_count(text)


# ============================================================
# Cache Manager
# ============================================================
class CacheManager:
    """Manages TTL caches for different analysis types."""
    
    def __init__(self):
        if CACHE_AVAILABLE and settings.enable_cache:
            self.grammar_cache = TTLCache(
                maxsize=settings.cache_maxsize,
                ttl=settings.cache_ttl
            )
            self.spell_cache = TTLCache(
                maxsize=settings.cache_maxsize,
                ttl=settings.cache_ttl
            )
            self.readability_cache = TTLCache(
                maxsize=settings.cache_maxsize,
                ttl=settings.cache_ttl
            )
            self.enabled = True
        else:
            self.enabled = False
    
    def get(self, cache_type: str, key: str) -> Optional[Any]:
        if not self.enabled:
            return None
        cache = getattr(self, f"{cache_type}_cache", None)
        return cache.get(key) if cache else None
    
    def set(self, cache_type: str, key: str, value: Any):
        if not self.enabled:
            return
        cache = getattr(self, f"{cache_type}_cache", None)
        if cache is not None:
            cache[key] = value


# ============================================================
# Service Layer (Singletons with lazy loading)
# ============================================================
class ServiceManager:
    """Manages singleton instances of analysis services."""
    
    def __init__(self):
        self._grammar_tool: Optional[language_tool_python.LanguageTool] = None
        self._sentiment_analyzer: Optional[SentimentIntensityAnalyzer] = None
        self._spell_checker: Optional[Tuple[str, Any]] = None
        self._http_client: Optional[httpx.AsyncClient] = None
        self._locks = {
            'grammar': asyncio.Lock(),
            'sentiment': asyncio.Lock(),
            'spell': asyncio.Lock(),
        }
        self.cache = CacheManager()
    
    async def get_grammar_tool(self) -> language_tool_python.LanguageTool:
        if self._grammar_tool:
            return self._grammar_tool
        
        async with self._locks['grammar']:
            if self._grammar_tool:
                return self._grammar_tool
            
            logger.info("Initializing LanguageTool...")
            self._grammar_tool = await run_in_threadpool(
                language_tool_python.LanguageTool, "en-US"
            )
            logger.info("LanguageTool initialized")
            return self._grammar_tool
    
    async def get_sentiment_analyzer(self) -> SentimentIntensityAnalyzer:
        if self._sentiment_analyzer:
            return self._sentiment_analyzer
        
        async with self._locks['sentiment']:
            if self._sentiment_analyzer:
                return self._sentiment_analyzer
            
            logger.info("Initializing VADER sentiment analyzer...")
            self._sentiment_analyzer = SentimentIntensityAnalyzer()
            logger.info("VADER initialized")
            return self._sentiment_analyzer
    
    async def get_spell_checker(self) -> Tuple[str, Any]:
        if self._spell_checker:
            return self._spell_checker
        
        async with self._locks['spell']:
            if self._spell_checker:
                return self._spell_checker
            
            # Try SymSpell first (faster)
            if SYMSPELL_AVAILABLE:
                try:
                    logger.info("Initializing SymSpell...")
                    def init_symspell():
                        sym = SymSpell(
                            max_dictionary_edit_distance=settings.spell_max_edit_distance,
                            prefix_length=7
                        )
                        if settings.symspell_freq_path and os.path.exists(settings.symspell_freq_path):
                            sym.load_dictionary(
                                settings.symspell_freq_path,
                                term_index=0,
                                count_index=1
                            )
                        else:
                            # Use default dictionary
                            sym.create_dictionary_entry("", 1)
                        return sym
                    
                    sym = await run_in_threadpool(init_symspell)
                    self._spell_checker = ("symspell", sym)
                    logger.info("SymSpell initialized")
                    return self._spell_checker
                except Exception as e:
                    logger.warning(f"SymSpell initialization failed: {e}")
            
            # Fallback to PySpellChecker
            if PYSPELL_AVAILABLE:
                logger.info("Initializing PySpellChecker...")
                checker = SpellChecker(language="en")
                self._spell_checker = ("pyspell", checker)
                logger.info("PySpellChecker initialized")
                return self._spell_checker
            
            # No spell checker available
            logger.warning("No spell checker available")
            self._spell_checker = ("none", None)
            return self._spell_checker
    
    def get_http_client(self) -> httpx.AsyncClient:
        if not self._http_client:
            self._http_client = httpx.AsyncClient(
                timeout=httpx.Timeout(settings.remote_timeout),
                limits=httpx.Limits(max_keepalive_connections=5, max_connections=10)
            )
        return self._http_client
    
    async def cleanup(self):
        """Cleanup resources."""
        if self._grammar_tool:
            try:
                self._grammar_tool.close()
            except Exception as e:
                logger.error(f"Error closing grammar tool: {e}")
        
        if self._http_client:
            await self._http_client.aclose()


# ============================================================
# Analysis Functions
# ============================================================
async def spell_corrections(text: str, service_mgr: ServiceManager) -> Dict[str, Any]:
    """Perform spell checking with caching."""
    text_hash = compute_text_hash(text)
    cached = service_mgr.cache.get('spell', text_hash)
    if cached:
        logger.debug("Spell check cache hit")
        return cached
    
    mode, checker = await service_mgr.get_spell_checker()
    
    def _check_spelling():
        tokens = tokenize_preserving_indices(text)
        corrections = []
        corrected_list = list(text)
        
        if mode == "none":
            return {"corrected_text": text, "corrections": []}
        
        for tok, start, end in tokens:
            # Skip if token is not alphabetic or too short
            if not tok.isalpha() or len(tok) < 2:
                continue
            
            best = None
            suggestions = []
            
            if mode == "symspell":
                results = checker.lookup(tok, Verbosity.TOP, max_edit_distance=2)
                if results and results[0].term.lower() != tok.lower():
                    best = results[0].term
                    suggestions = [r.term for r in results[:5]]
            
            elif mode == "pyspell":
                if checker.unknown([tok]):
                    best = checker.correction(tok)
                    candidates = checker.candidates(tok)
                    suggestions = list(candidates)[:5] if candidates else []
            
            if best and best != tok:
                corrected_list[start:end] = best
                corrections.append({
                    "token": tok,
                    "best": best,
                    "suggestions": suggestions,
                    "start": start,
                    "end": start + len(best),
                })
        
        return {
            "corrected_text": "".join(corrected_list),
            "corrections": corrections
        }
    
    result = await run_in_threadpool(_check_spelling)
    service_mgr.cache.set('spell', text_hash, result)
    return result


async def grammar_check(text: str, service_mgr: ServiceManager) -> List[Dict[str, Any]]:
    """Perform grammar checking with caching."""
    text_hash = compute_text_hash(text)
    cached = service_mgr.cache.get('grammar', text_hash)
    if cached:
        logger.debug("Grammar check cache hit")
        return cached
    
    tool = await service_mgr.get_grammar_tool()
    
    def _check_grammar():
        try:
            matches = tool.check(text)
            return [
                {
                    "message": m.message,
                    "context": m.context,
                    "suggestions": m.replacements[:5],
                    "rule_id": m.ruleId,
                    "offset": m.offset,
                    "length": m.errorLength,
                    "severity": "error" if "error" in m.ruleId.lower() else "suggestion"
                }
                for m in matches
            ]
        except Exception as e:
            logger.error(f"Grammar check failed: {e}")
            return []
    
    result = await asyncio.wait_for(
        run_in_threadpool(_check_grammar),
        timeout=settings.grammar_check_timeout
    )
    service_mgr.cache.set('grammar', text_hash, result)
    return result


async def readability_check(text: str, service_mgr: ServiceManager) -> Dict[str, Any]:
    """Perform readability analysis with caching."""
    text_hash = compute_text_hash(text)
    cached = service_mgr.cache.get('readability', text_hash)
    if cached:
        logger.debug("Readability check cache hit")
        return cached
    
    def _analyze_readability():
        sentences = count_sentences(text)
        words = count_words(text)
        
        if sentences < settings.min_sentence_for_readability:
            return {
                "readability_score": None,
                "grade_level": None,
                "sentences": sentences,
                "words": words,
                "note": f"Need at least {settings.min_sentence_for_readability} sentences for reliable analysis"
            }
        
        try:
            flesch_kincaid = textstat.flesch_kincaid_grade(text)
            flesch_reading = textstat.flesch_reading_ease(text)
            
            # Interpret reading ease
            if flesch_reading >= 90:
                difficulty = "Very Easy"
            elif flesch_reading >= 80:
                difficulty = "Easy"
            elif flesch_reading >= 70:
                difficulty = "Fairly Easy"
            elif flesch_reading >= 60:
                difficulty = "Standard"
            elif flesch_reading >= 50:
                difficulty = "Fairly Difficult"
            elif flesch_reading >= 30:
                difficulty = "Difficult"
            else:
                difficulty = "Very Difficult"
            
            return {
                "readability_score": flesch_reading,
                "grade_level": flesch_kincaid,
                "difficulty": difficulty,
                "sentences": sentences,
                "words": words,
                "avg_words_per_sentence": round(words / sentences, 1) if sentences > 0 else 0
            }
        except Exception as e:
            logger.error(f"Readability calculation failed: {e}")
            return {
                "readability_score": None,
                "grade_level": None,
                "sentences": sentences,
                "words": words,
                "error": str(e)
            }
    
    result = await run_in_threadpool(_analyze_readability)
    service_mgr.cache.set('readability', text_hash, result)
    return result


async def tone_analysis(text: str, service_mgr: ServiceManager) -> Dict[str, Any]:
    """Perform sentiment/tone analysis."""
    words = count_words(text)
    
    if words < settings.min_words_for_tone:
        return {
            "tone": "Neutral",
            "score": 0.0,
            "confidence": "Low",
            "note": f"Need at least {settings.min_words_for_tone} words for reliable tone analysis"
        }
    
    analyzer = await service_mgr.get_sentiment_analyzer()
    sentiment = analyzer.polarity_scores(text)
    
    compound = sentiment.get("compound", 0.0)
    
    # Determine tone and confidence
    if compound >= 0.5:
        tone = "Very Positive"
        confidence = "High"
    elif compound >= 0.2:
        tone = "Positive"
        confidence = "Medium"
    elif compound > -0.2:
        tone = "Neutral"
        confidence = "Medium"
    elif compound > -0.5:
        tone = "Negative"
        confidence = "Medium"
    else:
        tone = "Very Negative"
        confidence = "High"
    
    return {
        "tone": tone,
        "score": round(compound, 3),
        "confidence": confidence,
        "breakdown": {
            "positive": round(sentiment.get("pos", 0.0), 3),
            "neutral": round(sentiment.get("neu", 0.0), 3),
            "negative": round(sentiment.get("neg", 0.0), 3)
        }
    }


async def summarize_text(text: str, service_mgr: ServiceManager) -> str:
    """Generate text summary using remote API."""
    normalized = normalize_text(text)
    word_count = count_words(normalized)
    
    # For very short text, just clean it up
    if word_count <= settings.short_text_word_threshold:
        # Remove redundant words
        cleaned = re.sub(
            r'(\b\w+\b)(?:\s+\1\b)+',
            r'\1',
            normalized,
            flags=re.IGNORECASE
        )
        if len(cleaned) <= 400:
            return cleaned
        return cleaned[:400].rsplit(' ', 1)[0] + '...'
    
    # Use remote summarization API
    try:
        client = service_mgr.get_http_client()
        headers = {}
        if settings.remote_summarizer_token:
            headers["Authorization"] = f"Bearer {settings.remote_summarizer_token}"
        
        response = await client.post(
            settings.remote_summarizer_url,
            headers=headers,
            json={"inputs": normalized[:1024]}  # Limit input size
        )
        response.raise_for_status()
        
        data = response.json()
        if isinstance(data, list) and data:
            summary = data[0].get("summary_text", "")
            if summary:
                return summary
        
        # Fallback to truncation
        return normalized[:400].rsplit(' ', 1)[0] + '...'
        
    except Exception as e:
        logger.error(f"Remote summarization failed: {e}")
        return normalized[:400].rsplit(' ', 1)[0] + '...'


def merge_corrections(
    grammar_issues: List[Dict],
    spell_corrections: List[Dict]
) -> List[CorrectionItem]:
    """Merge grammar and spelling corrections, avoiding duplicates."""
    merged = []
    
    # Add grammar issues first
    for g in grammar_issues:
        g_start = g.get("offset", 0)
        g_end = g_start + g.get("length", 0)
        
        # Find overlapping spell corrections
        overlapping_spells = [
            s for s in spell_corrections
            if not (s["end"] <= g_start or s["start"] >= g_end)
        ]
        
        # Combine suggestions
        suggestions = []
        if overlapping_spells:
            for s in overlapping_spells:
                if s.get("best"):
                    suggestions.append(s["best"])
                suggestions.extend(s.get("suggestions", [])[:2])
        suggestions.extend(g.get("suggestions", [])[:3])
        
        # Remove duplicates while preserving order
        seen = set()
        unique_suggestions = []
        for sug in suggestions:
            if sug.lower() not in seen:
                seen.add(sug.lower())
                unique_suggestions.append(sug)
        
        merged.append(CorrectionItem(
            message=g.get("message", ""),
            context=g.get("context", ""),
            suggestions=unique_suggestions[:5],
            rule_id=g.get("rule_id"),
            offset=g.get("offset"),
            length=g.get("length"),
            severity=g.get("severity", "suggestion")
        ))
    
    # Add non-overlapping spell corrections
    for s in spell_corrections:
        overlapped = any(
            not (s["end"] <= g.get("offset", 0) or 
                 s["start"] >= g.get("offset", 0) + g.get("length", 0))
            for g in grammar_issues
        )
        
        if not overlapped:
            merged.append(CorrectionItem(
                message=f"Possible spelling: '{s['token']}'",
                context=s.get("context", ""),
                suggestions=s.get("suggestions", [])[:5],
                offset=s.get("start"),
                length=s["end"] - s["start"],
                severity="suggestion"
            ))
    
    return merged


# ============================================================
# API Endpoints
# ============================================================
@app.post("/analyze", response_model=AnalysisResponse)
async def analyze_text_endpoint(
    payload: TextRequest,
    request: Request,
    background_tasks: BackgroundTasks
):
    """Main text analysis endpoint."""
    text = payload.text.strip()
    service_mgr: ServiceManager = request.app.state.service_mgr
    
    start_time = asyncio.get_event_loop().time()
    
    try:
        # Step 1: Spell check first
        spell_result = {"corrected_text": text, "corrections": []}
        if payload.include_spell:
            spell_result = await spell_corrections(text, service_mgr)
        
        corrected_text = spell_result["corrected_text"]
        
        # Step 2: Run other analyses concurrently
        tasks = {}
        
        if payload.include_grammar:
            tasks["grammar"] = asyncio.create_task(grammar_check(corrected_text, service_mgr))
        
        if payload.include_readability:
            tasks["readability"] = asyncio.create_task(readability_check(corrected_text, service_mgr))
        
        if payload.include_tone:
            tasks["tone"] = asyncio.create_task(tone_analysis(corrected_text, service_mgr))
        
        if payload.include_summary:
            tasks["summary"] = asyncio.create_task(summarize_text(corrected_text, service_mgr))
        
        # Gather results
        results = await asyncio.gather(*tasks.values(), return_exceptions=True)
        result_dict = dict(zip(tasks.keys(), results))
        
        # Handle any errors
        for key, result in result_dict.items():
            if isinstance(result, Exception):
                logger.error(f"{key} analysis failed: {result}")
                result_dict[key] = None
        
        # Step 3: Merge corrections
        grammar_issues = result_dict.get("grammar", []) or []
        final_corrections = merge_corrections(grammar_issues, spell_result["corrections"])
        
        # Calculate processing time
        processing_time = asyncio.get_event_loop().time() - start_time
        
        return AnalysisResponse(
            original_text=text,
            corrected_text=corrected_text,
            corrections=final_corrections,
            readability=result_dict.get("readability"),
            tone=result_dict.get("tone"),
            engagement={"summary": result_dict.get("summary")} if result_dict.get("summary") else None,
            metadata={
                "processing_time_seconds": round(processing_time, 3),
                "word_count": count_words(text),
                "character_count": len(text),
                "correction_count": len(final_corrections)
            }
        )
    
    except asyncio.TimeoutError:
        raise HTTPException(status_code=504, detail="Request timeout")
    except Exception as e:
        logger.exception("Analysis pipeline error")
        raise HTTPException(status_code=500, detail="Internal processing error")


@app.get("/health")
async def health_check():
    """Health check endpoint."""
    return {
        "status": "healthy",
        "version": "2.0.0",
        "cache_enabled": settings.enable_cache and CACHE_AVAILABLE
    }


@app.get("/stats")
async def get_stats(request: Request):
    """Get service statistics."""
    service_mgr: ServiceManager = request.app.state.service_mgr
    
    stats = {
        "grammar_tool_loaded": service_mgr._grammar_tool is not None,
        "sentiment_analyzer_loaded": service_mgr._sentiment_analyzer is not None,
        "spell_checker_loaded": service_mgr._spell_checker is not None,
    }
    
    if service_mgr._spell_checker:
        stats["spell_checker_type"] = service_mgr._spell_checker[0]
    
    return stats


# ============================================================
# Lifecycle Events
# ============================================================
@app.on_event("startup")
async def startup_event():
    """Initialize services on startup."""
    logger.info("Starting AI Writing Assistant API...")
    app.state.service_mgr = ServiceManager()
    
    # Pre-warm critical services
    try:
        await app.state.service_mgr.get_sentiment_analyzer()
        logger.info("Pre-warmed sentiment analyzer")
    except Exception as e:
        logger.error(f"Failed to pre-warm sentiment analyzer: {e}")
    
    logger.info("Startup complete")


@app.on_event("shutdown")
async def shutdown_event():
    """Cleanup on shutdown."""
    logger.info("Shutting down...")
    await app.state.service_mgr.cleanup()
    logger.info("Shutdown complete")
```
---





