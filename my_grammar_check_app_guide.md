
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
# optimized_main.py
import asyncio
from concurrent.futures import ThreadPoolExecutor
from typing import Any, Dict, List, Optional, Tuple
import os
import logging
import re

from fastapi import FastAPI, HTTPException
from fastapi.middleware.cors import CORSMiddleware
from pydantic import BaseModel, Field

import textstat
from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer
import language_tool_python
from transformers import pipeline

# Prefer a fast spellchecker; try symspellpy first, otherwise use pyspellchecker
try:
    from symspellpy import SymSpell, Verbosity
    SYMSPELL_AVAILABLE = True
except Exception:
    SYMSPELL_AVAILABLE = False

try:
    from spellchecker import SpellChecker
    PYSPELL_AVAILABLE = True
except Exception:
    PYSPELL_AVAILABLE = False

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

USE_GPU = os.environ.get("USE_GPU", "0") == "1"
SUMMARIZER_MODEL = os.environ.get("SUMMARIZER_MODEL", "facebook/bart-large-cnn")
SHORT_TEXT_WORD_THRESHOLD = int(os.environ.get("SHORT_TEXT_WORD_THRESHOLD", "20"))
MIN_SENTENCE_FOR_READABILITY = int(os.environ.get("MIN_SENTENCE_FOR_READABILITY", "3"))

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
        device = 0 if USE_GPU else -1
        logger.info(f"Loading summarizer model ({SUMMARIZER_MODEL}) on device {device}...")
        try:
            get_summarizer.instance = pipeline(
                "summarization",
                model=SUMMARIZER_MODEL,
                device=device
            )
            logger.info("Summarizer ready.")
        except Exception as e:
            logger.exception("Failed to load summarizer model; summarization will fallback to simple behavior.")
            get_summarizer.instance = None
    return get_summarizer.instance


def get_spell_checker():
    """
    Returns a tuple (checker_type, checker_instance)
    checker_type: "symspell"|"pyspell"|"none"
    """
    if not hasattr(get_spell_checker, "instance"):
        if SYMSPELL_AVAILABLE:
            try:
                # Create a minimal in-memory SymSpell instance (no external dictionary)
                sym = SymSpell(max_dictionary_edit_distance=2, prefix_length=7)
                # If you have a word frequency file you can load it here with sym.load_dictionary
                # We'll still use sym for suggestion capability; it works acceptably even without large dict.
                get_spell_checker.instance = ("symspell", sym)
                logger.info("Using SymSpell spellchecker.")
            except Exception:
                get_spell_checker.instance = None

        if not getattr(get_spell_checker, "instance", None) and PYSPELL_AVAILABLE:
            try:
                sp = SpellChecker(language='en')
                get_spell_checker.instance = ("pyspell", sp)
                logger.info("Using pyspellchecker for spell correction.")
            except Exception:
                get_spell_checker.instance = None

        if not getattr(get_spell_checker, "instance", None):
            logger.warning("No spellchecker available. Spelling suggestions will be limited.")
            get_spell_checker.instance = ("none", None)

    return get_spell_checker.instance

# -------- Request / Response ----------
class TextRequest(BaseModel):
    text: str = Field(..., min_length=1, max_length=MAX_INPUT_CHARS)


# -------- Utility helpers ----------
def tokenize_preserving_indices(text: str) -> List[Tuple[str, int, int]]:
    """
    Return list of (token, start_idx, end_idx)
    Splits on whitespace/punctuation but keeps indices to allow replacements.
    """
    tokens = []
    for match in re.finditer(r"\S+", text):
        tokens.append((match.group(0), match.start(), match.end()))
    return tokens


def simple_normalize_text(text: str) -> str:
    """Basic cleanup and normalization: space punctuation, fix repeated spaces, trim."""
    text = text.strip()
    text = re.sub(r"\s+", " ", text)
    # ensure punctuation spacing
    text = re.sub(r"\s+([,.;:!?])", r"\1", text)
    return text


# -------- Spell correction (sync) ----------
def _spell_corrections_sync(text: str) -> Dict[str, Any]:
    """
    Returns:
      {
        "corrected_text": str,
        "corrections": [
           {"token": "youg", "start": 8, "end": 12, "suggestions": ["young", "your"], "best": "young"},
           ...
        ]
      }
    """
    checker_type, checker = get_spell_checker()
    tokens = tokenize_preserving_indices(text)
    corrections = []
    corrected_text = text

    if checker_type == "symspell":
        sym: SymSpell = checker  # type: ignore
        # Use symspell's lookup for each token
        # Note: Without a dictionary symspell may not be very useful; keep fallback behavior.
        offset_adjust = 0
        for token, start, end in tokens:
            # ignore punctuation/numeric tokens
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
                # Replace at positions (careful with prior replacements)
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
        sp: SpellChecker = checker  # type: ignore
        # pyspell works on lowercased tokens; we'll preserve case on replacement where possible.
        offset_adjust = 0
        for token, start, end in tokens:
            if re.fullmatch(r"^[\W_]+$", token) or re.fullmatch(r"^\d+$", token):
                continue
            # treat words with letters only
            raw = re.sub(r"^[^A-Za-z0-9']+|[^A-Za-z0-9']+$", "", token)
            if not raw:
                continue
            # check if word is unknown
            if raw.lower() in sp:
                continue
            # get candidates
            candidates = list(sp.candidates(raw))
            suggestions = sorted(candidates, key=lambda c: sp.word_probability(c), reverse=True) if candidates else []
            best = suggestions[0] if suggestions else None
            if best and best.lower() != raw.lower():
                real_start = start + offset_adjust
                real_end = end + offset_adjust
                # preserve capitalization
                if raw[0].isupper():
                    best_cased = best.capitalize()
                else:
                    best_cased = best
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
        # No spellchecker available: return original text with empty corrections
        return {"corrected_text": text, "corrections": []}

    return {"corrected_text": corrected_text, "corrections": corrections}


# -------- Grammar check (sync) ----------
def _grammar_sync(text: str) -> List[Dict[str, Any]]:
    tool = get_grammar_tool()
    matches = tool.check(text)
    results = []
    for m in matches:
        # obtain offsets when possible; some LanguageTool versions expose offset/length
        start = getattr(m, "offset", None)
        length = getattr(m, "errorLength", None) or getattr(m, "length", None)
        if start is not None and length is not None:
            start = int(start)
            length = int(length)
            end = start + length
            context = text[max(0, start - 30):min(len(text), end + 30)]
        else:
            # fallback: use the sentence or surrounding snippet
            sentence = getattr(m, "context", "") or getattr(m, "message", "")
            # try to extract a short window from the message itself
            context = sentence if sentence else text[:120]

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


# -------- Summarization (sync) ----------
def _summarize_sync(text: str) -> str:
    """
    For short inputs, return a normalized, corrected short restatement instead of running a full summarizer.
    For longer inputs, call the model if available; otherwise return the normalized first 200 chars.
    """
    summarizer = get_summarizer()
    word_count = len(re.findall(r"\w+", text))
    normalized = simple_normalize_text(text)

    if word_count <= SHORT_TEXT_WORD_THRESHOLD or summarizer is None:
        # For very short or malformed text, return normalized text as 'summary'
        # Also attempt to make a short clarity rewrite: collapse repeated tokens, fix spacing.
        clarity = re.sub(r"(\b\w+\b)(?:\s+\1\b)+", r"\1", normalized, flags=re.IGNORECASE)
        # Truncate to reasonable length
        return clarity if len(clarity) < 400 else clarity[:400].rsplit(" ", 1)[0] + "..."
    else:
        # Use the transformer summarizer
        try:
            res = summarizer(normalized, do_sample=False, truncation=True)
            if isinstance(res, list) and res:
                return res[0].get("summary_text", normalized[:400])
            return normalized[:400]
        except Exception:
            logger.exception("Summarizer model failed; returning normalized truncation.")
            return normalized[:400]


# -------- Async wrappers ----------
async def grammar_check(text: str):
    loop = asyncio.get_running_loop()
    return await loop.run_in_executor(executor, _grammar_sync, text)


async def readability_check(text: str):
    """
    Return readability metrics with guards on very short texts.
    """
    # Use executor to avoid blocking
    def _calc(text_local: str):
        sentences = max(1, len(re.findall(r"[.!?]+", text_local)))
        if sentences < MIN_SENTENCE_FOR_READABILITY or len(text_local.split()) < 5:
            return {
                "flesch_reading_ease": None,
                "grade_level": None,
                "reading_time_minutes": round(max(0.0, textstat.reading_time(text_local)), 2) if hasattr(textstat, 'reading_time') else None,
                "note": "Not enough content to compute reliable readability scores."
            }
        return {
            "flesch_reading_ease": textstat.flesch_reading_ease(text_local),
            "grade_level": textstat.flesch_kincaid_grade(text_local),
            "reading_time_minutes": round(textstat.reading_time(text_local), 2),
        }
    loop = asyncio.get_running_loop()
    return await loop.run_in_executor(executor, _calc, text)


async def tone_analysis(text: str):
    analyzer = get_sentiment_analyzer()
    # For very short inputs, skip and return insufficient data
    words = re.findall(r"\w+", text)
    if len(words) < 3:
        return {"tone": None, "score": None, "note": "Not enough text to determine tone reliably."}
    sentiment = analyzer.polarity_scores(text)
    score = sentiment.get("compound", 0.0)
    tone = "Positive" if score > 0.2 else "Negative" if score < -0.2 else "Neutral"
    return {"tone": tone, "score": score}


async def engagement_check(text: str):
    loop = asyncio.get_running_loop()
    return await loop.run_in_executor(executor, _summarize_sync, text)


async def spell_corrections(text: str):
    loop = asyncio.get_running_loop()
    return await loop.run_in_executor(executor, _spell_corrections_sync, text)


# -------- API endpoint ----------
@app.post("/analyze")
async def analyze_text(payload: TextRequest):
    raw_text = payload.text or ""
    text = raw_text.strip()
    if not text:
        raise HTTPException(status_code=400, detail="Text cannot be empty.")

    # Run spell corrections first, then grammar on corrected text
    try:
        # perform spell corrections (sync inside executor)
        spell_result = await spell_corrections(text)
        corrected_text = spell_result.get("corrected_text", text)
        spell_items = spell_result.get("corrections", [])

        # Run other analyses in parallel using corrected_text
        tasks = [
            asyncio.create_task(grammar_check(corrected_text)),
            asyncio.create_task(readability_check(corrected_text)),
            asyncio.create_task(tone_analysis(corrected_text)),
            asyncio.create_task(engagement_check(corrected_text)),
        ]
        grammar, readability, tone, engagement = await asyncio.gather(*tasks)

        # Merge spelling suggestions into the grammar corrections where appropriate:
        # If a grammar match overlaps a spelling correction, prefer the spelling correction as primary suggestion.
        # (This improves UX: user sees spelling fixes first.)
        merged_corrections = []
        for g in grammar:
            g_start = g.get("offset")
            g_len = g.get("length")
            g_end = g_start + g_len if (g_start is not None and g_len is not None) else None
            overlapping_spells = []
            for s in spell_items:
                s_start = s.get("start")
                s_end = s.get("end")
                if (g_start is not None and g_end is not None and s_start is not None and s_end is not None
                        and not (s_end <= g_start or s_start >= g_end)):
                    overlapping_spells.append(s)
            # prefer spell suggestions if present
            if overlapping_spells:
                # build a combined suggestions list (spell best first, then language tool suggestions)
                combined_suggestions = []
                for s in overlapping_spells:
                    # include best and top few suggestions
                    if s.get("best"):
                        combined_suggestions.append(s["best"])
                    combined_suggestions.extend(s.get("suggestions", [])[:3])
                # Add LanguageTool suggestions after
                combined_suggestions.extend(g.get("suggestions", [])[:3])
                merged = {
                    "message": g.get("message"),
                    "context": g.get("context"),
                    "suggestions": combined_suggestions,
                    "rule_id": g.get("rule_id"),
                    "offset": g.get("offset"),
                    "length": g.get("length"),
                }
                merged_corrections.append(merged)
            else:
                # No overlap, include as-is but ensure suggestions is present
                merged_corrections.append({
                    "message": g.get("message"),
                    "context": g.get("context"),
                    "suggestions": g.get("suggestions", []),
                    "rule_id": g.get("rule_id"),
                    "offset": g.get("offset"),
                    "length": g.get("length"),
                })

        # Also report spelling corrections that didn't overlap with grammar suggestions
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
                    "context": corrected_text[max(0, s.get("start", 0)-30): s.get("end", 0)+30],
                    "suggestions": s.get("suggestions", [])[:5],
                    "best": s.get("best"),
                    "offset": s.get("start"),
                    "length": (s.get("end") - s.get("start")) if (s.get("end") is not None and s.get("start") is not None) else None,
                })

        final_corrections = merged_corrections + non_overlapping_spells

        response = {
            "original_text": raw_text,
            "corrected_text": corrected_text,
            "corrections": final_corrections,
            "readability": readability,
            "tone": tone,
            "engagement": {"summary": engagement},
        }
        return response

    except Exception as e:
        logger.exception("Error during analysis pipeline")
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



