
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
- `fastapi.middleware.cors` (included within FastAPI) to enable CORS so the React frontend can access the API

We are going to install the libraries using this command e.g:
```bash
pip install --upgrade pip
pip install fastapi  
```
Also install other libraries 

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

app = FastAPI()
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_methods=["*"],
    allow_headers=["*"],
)

# Load heavy models only once
tool = language_tool_python.LanguageTool('en-US')
analyzer = SentimentIntensityAnalyzer()
summarizer = pipeline("summarization", model="facebook/bart-large-cnn")

async def grammar_check(text):
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
    sentiment = analyzer.polarity_scores(text)
    tone = "Positive" if sentiment['compound'] > 0.2 else "Negative" if sentiment['compound'] < -0.2 else "Neutral"
    return {"tone": tone, "score": sentiment['compound']}

async def engagement_check(text):
    summary = summarizer(text, max_length=30, min_length=5, do_sample=False)
    return {"summary": summary[0]['summary_text']}

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
uvicorn main:app --reload
```
* `main`: refers to the name of your Python file (e.g., `main.py`).
* `app`: refers to the FastAPI instance you created within that file (e.g., `app = FastAPI()`).
* `--reload`: (optional) enables automatic reloading of the server when code changes are detected, which is useful during development.  

**Verify the fastapi Server is running**
Once running, you can verify your server by checking the interactive fastapi documentation. i.e  

Open your browser and navigate to: http://127.0.0.1:8000/docs. This will confirm the API is up and ready to receive requests from your frontend  

Ensure `fastapi` and `uvicorn` are installed for the backend server to run.  

Bash

pip install fastapi uvicorn
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






