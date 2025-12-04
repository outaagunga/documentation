
# Grammarly like app for writing and Grammar check

# ✅ **Recommended Project Structure**

```
project/
│
├── backend/
│   ├── app.py
│   ├── routers/
│   │   ├── __init__.py
│   │   ├── spelling_check.py
│   │   ├── grammar_check.py
│   │   ├── tone_check.py
│   │   ├── readability_check.py
│   │   ├── ai_rewrite.py
│   │
│   ├── services/
│   │   ├── spelling_service.py
│   │   ├── grammar_service.py
│   │   ├── tone_service.py
│   │   ├── readability_service.py
│   │   ├── ai_rewrite_service.py
│   │
│   ├── utils/
│   │   ├── lazy_loading.py
│   │   ├── validation.py
│   │   ├── settings.py
│   │
│   ├── models/
│   │   ├── request_models.py
│   │   ├── response_models.py
│
└── frontend/
    └── (later)
```

# ⭐ Tips To achieve Clean Codebase

### ✔ Keep endpoints short

Endpoints should only handle HTTP logic and call service functions.

### ✔ Put heavy logic in `services/`

Makes the app testable and clean.

### ✔ Use `models/` for request/response validation

This prevents mixing validation with business logic.

### ✔ Use `utils/` for helpers

Lazy loading ML models, configuration, shared functions.

---
## Each task should be in its own file categorized in sub-directories
✔ `routers/` = endpoints
✔ `services/` = logic
✔ `models/` = request/response schemas
✔ `utils/` = lazy-loading, settings, validation
✔ `app.py` = the main FastAPI app

---

# 📁 **project/backend/app.py**

```python
from fastapi import FastAPI
from routers import (
    spelling_check,
    grammar_check,
    tone_check,
    readability_check,
    ai_rewrite
)

app = FastAPI(
    title="Grammarly-like Backend",
    version="1.0.0"
)

# Register all routers
app.include_router(spelling_check.router, prefix="/spelling", tags=["Spelling"])
app.include_router(grammar_check.router, prefix="/grammar", tags=["Grammar"])
app.include_router(tone_check.router, prefix="/tone", tags=["Tone"])
app.include_router(readability_check.router, prefix="/readability", tags=["Readability"])
app.include_router(ai_rewrite.router, prefix="/rewrite", tags=["AI Rewrite"])

@app.get("/")
def root():
    return {"message": "Backend running successfully!"}
```

---

# 📁 **routers/__init__.py**

```python
# Enables importing routers as a package
```

---

# 📁 **routers/spelling_check.py**

```python
from fastapi import APIRouter
from models.request_models import TextRequest
from services.spelling_service import check_spelling

router = APIRouter()

@router.post("/")
def spelling_endpoint(data: TextRequest):
    result = check_spelling(data.text)
    return {"spelling_issues": result}
```

---

# 📁 **routers/grammar_check.py**

```python
from fastapi import APIRouter
from models.request_models import TextRequest
from services.grammar_service import check_grammar

router = APIRouter()

@router.post("/")
def grammar_endpoint(data: TextRequest):
    result = check_grammar(data.text)
    return {"grammar_issues": result}
```

---

# 📁 **routers/tone_check.py**

```python
from fastapi import APIRouter
from models.request_models import TextRequest
from services.tone_service import analyze_tone

router = APIRouter()

@router.post("/")
def tone_endpoint(data: TextRequest):
    result = analyze_tone(data.text)
    return {"tone": result}
```

---

# 📁 **routers/readability_check.py**

```python
from fastapi import APIRouter
from models.request_models import TextRequest
from services.readability_service import check_readability

router = APIRouter()

@router.post("/")
def readability_endpoint(data: TextRequest):
    result = check_readability(data.text)
    return {"readability_score": result}
```

---

# 📁 **routers/ai_rewrite.py**

```python
from fastapi import APIRouter
from models.request_models import TextRequest
from services.ai_rewrite_service import rewrite_text

router = APIRouter()

@router.post("/")
def ai_rewrite_endpoint(data: TextRequest):
    result = rewrite_text(data.text)
    return {"rewritten_text": result}
```

---

# 📁 **services/spelling_service.py**

```python
def check_spelling(text: str):
    # Example placeholder logic (replace with real model later)
    issues = []
    if "teh" in text.lower():
        issues.append({"word": "teh", "suggestion": "the"})
    return issues
```

---

# 📁 **services/grammar_service.py**

```python
def check_grammar(text: str):
    issues = []
    if "is are" in text.lower():
        issues.append({
            "error": "is are",
            "suggestion": "is" if "is" else "are"
        })
    return issues
```

---

# 📁 **services/tone_service.py**

```python
def analyze_tone(text: str):
    lowered = text.lower()
    if "!" in text:
        return "Excited / Energetic"
    elif any(word in lowered for word in ["sorry", "apologize"]):
        return "Apologetic"
    else:
        return "Neutral"
```

---

# 📁 **services/readability_service.py**

```python
import textstat

def check_readability(text: str):
    score = textstat.flesch_reading_ease(text)
    return score
```

---

# 📁 **services/ai_rewrite_service.py**

```python
def rewrite_text(text: str):
    # Placeholder — replace with OpenAI or HuggingFace when ready
    return "Rewritten (placeholder): " + text.capitalize()
```

---

# 📁 **models/request_models.py**

```python
from pydantic import BaseModel, Field

class TextRequest(BaseModel):
    text: str = Field(..., min_length=1)
```

---

# 📁 **models/response_models.py**

```python
from pydantic import BaseModel

class RewriteResponse(BaseModel):
    rewritten_text: str
```

---

# 📁 **utils/validation.py**

```python
def validate_text(text: str):
    if not text or text.strip() == "":
        raise ValueError("Text cannot be empty.")
    return True
```

---

# 📁 **utils/lazy_loading.py**

```python
_cached_model = None

def load_model_once(load_function):
    global _cached_model
    if _cached_model is None:
        _cached_model = load_function()
    return _cached_model
```

---

# 📁 **utils/settings.py**

```python
class Settings:
    DEBUG = True
    MODEL_DIR = "models/"
    PROJECT_NAME = "Grammarly-like Backend"

settings = Settings()
```
---




