
I'll extend your Grammarly-like application structure with all the missing components needed for a real-world application, while preserving your learning-focused organization. Let me provide this in segments due to the extensive content.

## **SEGMENT 1: Complete Directory Structure + Core Application Files**

### **📁 Complete Directory Tree**

```
project/
│
├── backend/
│   ├── app.py
│   ├── main.py
│   ├── config.py
│   ├── dependencies.py
│   ├── __init__.py
│   │
│   ├── routers/
│   │   ├── __init__.py
│   │   ├── spelling_check.py
│   │   ├── grammar_check.py
│   │   ├── tone_check.py
│   │   ├── readability_check.py
│   │   ├── ai_rewrite.py
│   │   ├── health.py
│   │   ├── analytics.py
│   │   ├── user.py
│   │   ├── document.py
│   │
│   ├── services/
│   │   ├── __init__.py
│   │   ├── spelling_service.py
│   │   ├── grammar_service.py
│   │   ├── tone_service.py
│   │   ├── readability_service.py
│   │   ├── ai_rewrite_service.py
│   │   ├── text_analysis_service.py
│   │   ├── suggestion_service.py
│   │   ├── context_service.py
│   │   ├── plagiarism_service.py
│   │   ├── style_service.py
│   │
│   ├── models/
│   │   ├── __init__.py
│   │   ├── request_models.py
│   │   ├── response_models.py
│   │   ├── database_models.py
│   │   ├── enums.py
│   │   ├── error_models.py
│   │
│   ├── utils/
│   │   ├── __init__.py
│   │   ├── lazy_loading.py
│   │   ├── validation.py
│   │   ├── settings.py
│   │   ├── text_processing.py
│   │   ├── logger.py
│   │   ├── cache.py
│   │   ├── rate_limiter.py
│   │   ├── error_handler.py
│   │   ├── metrics.py
│   │
│   ├── core/
│   │   ├── __init__.py
│   │   ├── security.py
│   │   ├── database.py
│   │   ├── middleware.py
│   │   ├── exceptions.py
│   │
│   ├── ml_models/
│   │   ├── __init__.py
│   │   ├── model_loader.py
│   │   ├── grammar_model.py
│   │   ├── tone_model.py
│   │   ├── spell_checker.py
│   │   ├── style_analyzer.py
│   │
│   ├── database/
│   │   ├── __init__.py
│   │   ├── connection.py
│   │   ├── repositories/
│   │   │   ├── __init__.py
│   │   │   ├── user_repository.py
│   │   │   ├── document_repository.py
│   │   │   ├── suggestion_repository.py
│   │
│   ├── api/
│   │   ├── __init__.py
│   │   ├── dependencies.py
│   │   ├── middleware.py
│   │
│   ├── tests/
│   │   ├── __init__.py
│   │   ├── conftest.py
│   │   ├── test_spelling.py
│   │   ├── test_grammar.py
│   │   ├── test_tone.py
│   │   ├── test_readability.py
│   │   ├── test_services.py
│   │
│   ├── migrations/
│   │   ├── versions/
│   │   ├── env.py
│   │   ├── alembic.ini
│   │
│   ├── static/
│   │   └── models/
│   │
│   ├── logs/
│   │   └── .gitkeep
│   │
│   ├── requirements.txt
│   ├── .env.example
│   ├── .env
│   ├── .gitignore
│   ├── Dockerfile
│   ├── docker-compose.yml
│   ├── README.md
│
└── frontend/
    └── (future implementation)
```

---

### **📁 backend/app.py**

```python
"""
Main FastAPI Application
This file initializes the FastAPI app and registers all routers.
"""
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from fastapi.middleware.gzip import GZipMiddleware
from contextlib import asynccontextmanager

from routers import (
    spelling_check,
    grammar_check,
    tone_check,
    readability_check,
    ai_rewrite,
    health,
    analytics,
    user,
    document
)
from core.middleware import (
    LoggingMiddleware,
    RateLimitMiddleware,
    RequestIDMiddleware
)
from core.exceptions import (
    register_exception_handlers
)
from core.database import init_db, close_db
from utils.logger import setup_logger
from utils.settings import settings

# Setup logging
logger = setup_logger(__name__)


@asynccontextmanager
async def lifespan(app: FastAPI):
    """
    Lifespan event handler for startup and shutdown events.
    Manages database connections and ML model loading.
    """
    # Startup
    logger.info("Starting up application...")
    try:
        await init_db()
        logger.info("Database initialized successfully")
    except Exception as e:
        logger.error(f"Failed to initialize database: {e}")
        raise
    
    yield
    
    # Shutdown
    logger.info("Shutting down application...")
    await close_db()
    logger.info("Application shutdown complete")


# Initialize FastAPI app
app = FastAPI(
    title=settings.PROJECT_NAME,
    version=settings.VERSION,
    description="A comprehensive writing assistant API similar to Grammarly",
    docs_url="/docs" if settings.DEBUG else None,
    redoc_url="/redoc" if settings.DEBUG else None,
    lifespan=lifespan
)

# CORS Configuration
app.add_middleware(
    CORSMiddleware,
    allow_origins=settings.ALLOWED_ORIGINS,
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# Add compression middleware
app.add_middleware(GZipMiddleware, minimum_size=1000)

# Custom middleware
app.add_middleware(RequestIDMiddleware)
app.add_middleware(LoggingMiddleware)
app.add_middleware(RateLimitMiddleware)

# Register exception handlers
register_exception_handlers(app)

# Register all routers
app.include_router(health.router, prefix="/health", tags=["Health"])
app.include_router(spelling_check.router, prefix="/api/v1/spelling", tags=["Spelling"])
app.include_router(grammar_check.router, prefix="/api/v1/grammar", tags=["Grammar"])
app.include_router(tone_check.router, prefix="/api/v1/tone", tags=["Tone"])
app.include_router(readability_check.router, prefix="/api/v1/readability", tags=["Readability"])
app.include_router(ai_rewrite.router, prefix="/api/v1/rewrite", tags=["AI Rewrite"])
app.include_router(analytics.router, prefix="/api/v1/analytics", tags=["Analytics"])
app.include_router(user.router, prefix="/api/v1/users", tags=["Users"])
app.include_router(document.router, prefix="/api/v1/documents", tags=["Documents"])


@app.get("/")
async def root():
    """Root endpoint - API information"""
    return {
        "message": "Grammarly-like Backend API",
        "version": settings.VERSION,
        "status": "running",
        "docs": "/docs" if settings.DEBUG else "Documentation disabled in production"
    }


@app.get("/api/v1")
async def api_info():
    """API version information"""
    return {
        "version": "1.0.0",
        "endpoints": {
            "spelling": "/api/v1/spelling",
            "grammar": "/api/v1/grammar",
            "tone": "/api/v1/tone",
            "readability": "/api/v1/readability",
            "rewrite": "/api/v1/rewrite",
            "analytics": "/api/v1/analytics",
            "users": "/api/v1/users",
            "documents": "/api/v1/documents"
        }
    }
```

---

### **📁 backend/main.py**

```python
"""
Entry point for running the application with Uvicorn
"""
import uvicorn
from utils.settings import settings
from utils.logger import setup_logger

logger = setup_logger(__name__)

if __name__ == "__main__":
    logger.info(f"Starting {settings.PROJECT_NAME} on {settings.HOST}:{settings.PORT}")
    
    uvicorn.run(
        "app:app",
        host=settings.HOST,
        port=settings.PORT,
        reload=settings.DEBUG,
        log_level="info" if settings.DEBUG else "warning",
        access_log=settings.DEBUG,
        workers=settings.WORKERS if not settings.DEBUG else 1
    )
```

---

### **📁 backend/config.py**

```python
"""
Configuration settings for the application
Centralized configuration management
"""
import os
from typing import List, Optional
from pydantic_settings import BaseSettings
from pydantic import Field, validator


class AppSettings(BaseSettings):
    """Application configuration settings"""
    
    # Application
    PROJECT_NAME: str = "Grammarly-like Backend"
    VERSION: str = "1.0.0"
    DEBUG: bool = Field(default=True, env="DEBUG")
    ENVIRONMENT: str = Field(default="development", env="ENVIRONMENT")
    
    # Server
    HOST: str = Field(default="0.0.0.0", env="HOST")
    PORT: int = Field(default=8000, env="PORT")
    WORKERS: int = Field(default=4, env="WORKERS")
    
    # CORS
    ALLOWED_ORIGINS: List[str] = Field(
        default=["http://localhost:3000", "http://localhost:8000"],
        env="ALLOWED_ORIGINS"
    )
    
    # Database
    DATABASE_URL: str = Field(
        default="postgresql://user:password@localhost:5432/grammarly_db",
        env="DATABASE_URL"
    )
    DB_POOL_SIZE: int = Field(default=10, env="DB_POOL_SIZE")
    DB_MAX_OVERFLOW: int = Field(default=20, env="DB_MAX_OVERFLOW")
    
    # Redis Cache
    REDIS_URL: str = Field(default="redis://localhost:6379/0", env="REDIS_URL")
    CACHE_TTL: int = Field(default=3600, env="CACHE_TTL")  # 1 hour
    
    # Security
    SECRET_KEY: str = Field(default="your-secret-key-change-in-production", env="SECRET_KEY")
    API_KEY_HEADER: str = "X-API-Key"
    JWT_ALGORITHM: str = "HS256"
    ACCESS_TOKEN_EXPIRE_MINUTES: int = 30
    
    # Rate Limiting
    RATE_LIMIT_PER_MINUTE: int = Field(default=60, env="RATE_LIMIT_PER_MINUTE")
    RATE_LIMIT_PER_HOUR: int = Field(default=1000, env="RATE_LIMIT_PER_HOUR")
    
    # AI Models
    OPENAI_API_KEY: Optional[str] = Field(default=None, env="OPENAI_API_KEY")
    HUGGINGFACE_API_KEY: Optional[str] = Field(default=None, env="HUGGINGFACE_API_KEY")
    MODEL_CACHE_DIR: str = Field(default="./static/models", env="MODEL_CACHE_DIR")
    
    # ML Model Settings
    GRAMMAR_MODEL_NAME: str = "prithivida/grammar_error_correcter_v1"
    TONE_MODEL_NAME: str = "cardiffnlp/twitter-roberta-base-sentiment"
    MAX_TEXT_LENGTH: int = Field(default=10000, env="MAX_TEXT_LENGTH")
    
    # Logging
    LOG_LEVEL: str = Field(default="INFO", env="LOG_LEVEL")
    LOG_FILE: str = Field(default="./logs/app.log", env="LOG_FILE")
    LOG_FORMAT: str = "%(asctime)s - %(name)s - %(levelname)s - %(message)s"
    
    # File Upload
    MAX_UPLOAD_SIZE: int = Field(default=10485760, env="MAX_UPLOAD_SIZE")  # 10MB
    ALLOWED_EXTENSIONS: List[str] = [".txt", ".doc", ".docx", ".pdf"]
    
    # Analytics
    ENABLE_ANALYTICS: bool = Field(default=True, env="ENABLE_ANALYTICS")
    ANALYTICS_BATCH_SIZE: int = Field(default=100, env="ANALYTICS_BATCH_SIZE")
    
    @validator("ALLOWED_ORIGINS", pre=True)
    def parse_origins(cls, v):
        if isinstance(v, str):
            return [origin.strip() for origin in v.split(",")]
        return v
    
    class Config:
        env_file = ".env"
        case_sensitive = True


# Global settings instance
settings = AppSettings()
```

---

### **📁 backend/dependencies.py**

```python
"""
FastAPI dependency injection functions
Shared dependencies used across multiple endpoints
"""
from typing import Optional
from fastapi import Depends, HTTPException, Header, status
from sqlalchemy.ext.asyncio import AsyncSession

from core.database import get_db
from core.security import verify_api_key, get_current_user
from models.database_models import User
from utils.cache import get_cache_client
from utils.rate_limiter import check_rate_limit
from utils.settings import settings


async def get_db_session() -> AsyncSession:
    """
    Dependency to get database session.
    Automatically handles session cleanup.
    """
    async for session in get_db():
        yield session


async def get_cache():
    """
    Dependency to get cache client.
    """
    return await get_cache_client()


async def verify_api_key_dependency(
    x_api_key: Optional[str] = Header(None, alias="X-API-Key")
) -> bool:
    """
    Verify API key from request header.
    Raises 401 if invalid or missing.
    """
    if not x_api_key:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="API key is missing"
        )
    
    if not await verify_api_key(x_api_key):
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid API key"
        )
    
    return True


async def get_current_active_user(
    current_user: User = Depends(get_current_user)
) -> User:
    """
    Get current authenticated user.
    Ensures user is active.
    """
    if not current_user.is_active:
        raise HTTPException(
            status_code=status.HTTP_403_FORBIDDEN,
            detail="Inactive user"
        )
    return current_user


async def rate_limit_dependency(
    x_api_key: Optional[str] = Header(None, alias="X-API-Key")
):
    """
    Check rate limits for the current user/API key.
    """
    identifier = x_api_key or "anonymous"
    
    if not await check_rate_limit(identifier):
        raise HTTPException(
            status_code=status.HTTP_429_TOO_MANY_REQUESTS,
            detail="Rate limit exceeded. Please try again later."
        )


def validate_text_length(text: str) -> str:
    """
    Validate text length doesn't exceed maximum.
    """
    if len(text) > settings.MAX_TEXT_LENGTH:
        raise HTTPException(
            status_code=status.HTTP_413_REQUEST_ENTITY_TOO_LARGE,
            detail=f"Text exceeds maximum length of {settings.MAX_TEXT_LENGTH} characters"
        )
    return text


class CommonQueryParams:
    """
    Common query parameters for pagination and filtering.
    """
    def __init__(
        self,
        skip: int = 0,
        limit: int = 100,
        sort_by: Optional[str] = None,
        order: str = "asc"
    ):
        self.skip = skip
        self.limit = min(limit, 1000)  # Max 1000 items per request
        self.sort_by = sort_by
        self.order = order
```

---

### **📁 backend/__init__.py**

```python
"""
Backend package initialization
"""
__version__ = "1.0.0"
__author__ = "Your Name"
__description__ = "Grammarly-like writing assistant backend API"
```

---

## **SEGMENT 2: Router Files (Existing + New)**

### **📁 routers/__init__.py**

```python
"""
Routers package - API endpoint handlers
All routers are imported here for easy access
"""
from . import (
    spelling_check,
    grammar_check,
    tone_check,
    readability_check,
    ai_rewrite,
    health,
    analytics,
    user,
    document
)

__all__ = [
    "spelling_check",
    "grammar_check",
    "tone_check",
    "readability_check",
    "ai_rewrite",
    "health",
    "analytics",
    "user",
    "document"
]
```

---

### **📁 routers/spelling_check.py**

```python
"""
Spelling Check Router
Handles endpoints for spelling correction and suggestions
"""
from fastapi import APIRouter, Depends, HTTPException, status
from typing import List

from models.request_models import TextRequest, BatchTextRequest
from models.response_models import SpellingCheckResponse, BatchSpellingCheckResponse
from services.spelling_service import (
    check_spelling,
    get_spelling_suggestions,
    check_spelling_batch
)
from dependencies import (
    verify_api_key_dependency,
    rate_limit_dependency,
    validate_text_length
)
from utils.logger import setup_logger

logger = setup_logger(__name__)
router = APIRouter()


@router.post(
    "/",
    response_model=SpellingCheckResponse,
    dependencies=[Depends(verify_api_key_dependency), Depends(rate_limit_dependency)]
)
async def spelling_endpoint(data: TextRequest):
    """
    Check spelling in the provided text.
    
    - **text**: The text to check for spelling errors
    
    Returns a list of spelling issues with suggestions.
    """
    try:
        validated_text = validate_text_length(data.text)
        result = await check_spelling(validated_text)
        
        logger.info(f"Spelling check completed: {len(result.issues)} issues found")
        
        return result
        
    except ValueError as e:
        logger.error(f"Validation error in spelling check: {e}")
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail=str(e)
        )
    except Exception as e:
        logger.error(f"Error in spelling check: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while checking spelling"
        )


@router.post(
    "/suggestions",
    dependencies=[Depends(verify_api_key_dependency)]
)
async def get_suggestions(word: str, max_suggestions: int = 5):
    """
    Get spelling suggestions for a specific word.
    
    - **word**: The word to get suggestions for
    - **max_suggestions**: Maximum number of suggestions to return
    """
    try:
        suggestions = await get_spelling_suggestions(word, max_suggestions)
        
        return {
            "word": word,
            "suggestions": suggestions,
            "count": len(suggestions)
        }
        
    except Exception as e:
        logger.error(f"Error getting spelling suggestions: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while getting suggestions"
        )


@router.post(
    "/batch",
    response_model=BatchSpellingCheckResponse,
    dependencies=[Depends(verify_api_key_dependency), Depends(rate_limit_dependency)]
)
async def spelling_batch_endpoint(data: BatchTextRequest):
    """
    Check spelling for multiple texts in a single request.
    
    - **texts**: List of texts to check
    
    Returns spelling check results for each text.
    """
    try:
        if len(data.texts) > 100:
            raise ValueError("Maximum 100 texts per batch request")
        
        results = await check_spelling_batch(data.texts)
        
        logger.info(f"Batch spelling check completed for {len(data.texts)} texts")
        
        return results
        
    except ValueError as e:
        logger.error(f"Validation error in batch spelling check: {e}")
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail=str(e)
        )
    except Exception as e:
        logger.error(f"Error in batch spelling check: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while checking spelling"
        )
```

---

### **📁 routers/grammar_check.py**

```python
"""
Grammar Check Router
Handles endpoints for grammar checking and correction
"""
from fastapi import APIRouter, Depends, HTTPException, status, Query
from typing import Optional

from models.request_models import TextRequest, GrammarCheckRequest
from models.response_models import GrammarCheckResponse
from models.enums import GrammarRuleType
from services.grammar_service import (
    check_grammar,
    check_grammar_detailed,
    apply_grammar_correction
)
from dependencies import (
    verify_api_key_dependency,
    rate_limit_dependency,
    validate_text_length
)
from utils.logger import setup_logger

logger = setup_logger(__name__)
router = APIRouter()


@router.post(
    "/",
    response_model=GrammarCheckResponse,
    dependencies=[Depends(verify_api_key_dependency), Depends(rate_limit_dependency)]
)
async def grammar_endpoint(data: TextRequest):
    """
    Check grammar in the provided text.
    
    - **text**: The text to check for grammar errors
    
    Returns a list of grammar issues with corrections.
    """
    try:
        validated_text = validate_text_length(data.text)
        result = await check_grammar(validated_text)
        
        logger.info(f"Grammar check completed: {len(result.issues)} issues found")
        
        return result
        
    except ValueError as e:
        logger.error(f"Validation error in grammar check: {e}")
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail=str(e)
        )
    except Exception as e:
        logger.error(f"Error in grammar check: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while checking grammar"
        )


@router.post(
    "/detailed",
    dependencies=[Depends(verify_api_key_dependency), Depends(rate_limit_dependency)]
)
async def grammar_detailed_endpoint(
    data: GrammarCheckRequest,
    include_explanations: bool = Query(True, description="Include explanations for each issue")
):
    """
    Perform detailed grammar check with rule explanations.
    
    - **text**: The text to analyze
    - **rule_types**: Optional list of specific grammar rule types to check
    - **include_explanations**: Whether to include detailed explanations
    """
    try:
        validated_text = validate_text_length(data.text)
        result = await check_grammar_detailed(
            validated_text,
            rule_types=data.rule_types,
            include_explanations=include_explanations
        )
        
        return result
        
    except Exception as e:
        logger.error(f"Error in detailed grammar check: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while performing detailed grammar check"
        )


@router.post(
    "/correct",
    dependencies=[Depends(verify_api_key_dependency)]
)
async def correct_grammar(data: TextRequest, auto_apply: bool = True):
    """
    Automatically correct grammar issues in text.
    
    - **text**: The text to correct
    - **auto_apply**: Whether to automatically apply all corrections
    """
    try:
        validated_text = validate_text_length(data.text)
        result = await apply_grammar_correction(validated_text, auto_apply)
        
        logger.info("Grammar correction applied successfully")
        
        return result
        
    except Exception as e:
        logger.error(f"Error in grammar correction: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while correcting grammar"
        )
```

---

### **📁 routers/tone_check.py**

```python
"""
Tone Analysis Router
Handles endpoints for tone detection and analysis
"""
from fastapi import APIRouter, Depends, HTTPException, status, Query
from typing import Optional, List

from models.request_models import TextRequest, ToneAnalysisRequest
from models.response_models import ToneAnalysisResponse, ToneSuggestionResponse
from models.enums import ToneType
from services.tone_service import (
    analyze_tone,
    analyze_tone_detailed,
    get_tone_suggestions,
    compare_tones
)
from dependencies import (
    verify_api_key_dependency,
    rate_limit_dependency,
    validate_text_length
)
from utils.logger import setup_logger

logger = setup_logger(__name__)
router = APIRouter()


@router.post(
    "/",
    response_model=ToneAnalysisResponse,
    dependencies=[Depends(verify_api_key_dependency), Depends(rate_limit_dependency)]
)
async def tone_endpoint(data: TextRequest):
    """
    Analyze the tone of the provided text.
    
    - **text**: The text to analyze
    
    Returns tone classification with confidence scores.
    """
    try:
        validated_text = validate_text_length(data.text)
        result = await analyze_tone(validated_text)
        
        logger.info(f"Tone analysis completed: {result.primary_tone}")
        
        return result
        
    except ValueError as e:
        logger.error(f"Validation error in tone analysis: {e}")
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail=str(e)
        )
    except Exception as e:
        logger.error(f"Error in tone analysis: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while analyzing tone"
        )


@router.post(
    "/detailed",
    dependencies=[Depends(verify_api_key_dependency)]
)
async def tone_detailed_endpoint(data: ToneAnalysisRequest):
    """
    Perform detailed tone analysis with sentence-level breakdown.
    
    - **text**: The text to analyze
    - **target_tone**: Optional target tone to compare against
    - **include_sentiment**: Include sentiment analysis
    """
    try:
        validated_text = validate_text_length(data.text)
        result = await analyze_tone_detailed(
            validated_text,
            target_tone=data.target_tone,
            include_sentiment=data.include_sentiment
        )
        
        return result
        
    except Exception as e:
        logger.error(f"Error in detailed tone analysis: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while performing detailed tone analysis"
        )


@router.post(
    "/suggestions",
    response_model=ToneSuggestionResponse,
    dependencies=[Depends(verify_api_key_dependency)]
)
async def tone_suggestions_endpoint(
    data: TextRequest,
    target_tone: ToneType = Query(..., description="Desired tone for the text")
):
    """
    Get suggestions to adjust text to match target tone.
    
    - **text**: The original text
    - **target_tone**: The desired tone (formal, casual, friendly, professional, etc.)
    """
    try:
        validated_text = validate_text_length(data.text)
        suggestions = await get_tone_suggestions(validated_text, target_tone)
        
        return suggestions
        
    except Exception as e:
        logger.error(f"Error getting tone suggestions: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while generating tone suggestions"
        )


@router.post(
    "/compare",
    dependencies=[Depends(verify_api_key_dependency)]
)
async def compare_tones_endpoint(
    original_text: str,
    modified_text: str
):
    """
    Compare the tone between original and modified text.
    
    - **original_text**: The original text
    - **modified_text**: The modified text to compare
    """
    try:
        result = await compare_tones(original_text, modified_text)
        
        return result
        
    except Exception as e:
        logger.error(f"Error comparing tones: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while comparing tones"
        )
```

---

### **📁 routers/readability_check.py**

```python
"""
Readability Check Router
Handles endpoints for readability analysis and scoring
"""
from fastapi import APIRouter, Depends, HTTPException, status, Query
from typing import Optional

from models.request_models import TextRequest, ReadabilityRequest
from models.response_models import ReadabilityResponse, ReadabilityDetailedResponse
from services.readability_service import (
    check_readability,
    check_readability_detailed,
    get_readability_suggestions,
    analyze_sentence_complexity
)
from dependencies import (
    verify_api_key_dependency,
    rate_limit_dependency,
    validate_text_length
)
from utils.logger import setup_logger

logger = setup_logger(__name__)
router = APIRouter()


@router.post(
    "/",
    response_model=ReadabilityResponse,
    dependencies=[Depends(verify_api_key_dependency), Depends(rate_limit_dependency)]
)
async def readability_endpoint(data: TextRequest):
    """
    Calculate readability scores for the provided text.
    
    - **text**: The text to analyze
    
    Returns multiple readability metrics (Flesch Reading Ease, Flesch-Kincaid Grade, etc.)
    """
    try:
        validated_text = validate_text_length(data.text)
        result = await check_readability(validated_text)
logger.info(f"Readability check completed: Score {result.flesch_reading_ease}")
        
        return result
        
    except ValueError as e:
        logger.error(f"Validation error in readability check: {e}")
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail=str(e)
        )
    except Exception as e:
        logger.error(f"Error in readability check: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while checking readability"
        )


@router.post(
    "/detailed",
    response_model=ReadabilityDetailedResponse,
    dependencies=[Depends(verify_api_key_dependency)]
)
async def readability_detailed_endpoint(data: ReadabilityRequest):
    """
    Perform detailed readability analysis with sentence-level metrics.
    
    - **text**: The text to analyze
    - **target_grade_level**: Optional target reading grade level
    """
    try:
        validated_text = validate_text_length(data.text)
        result = await check_readability_detailed(
            validated_text,
            target_grade_level=data.target_grade_level
        )
        
        return result
        
    except Exception as e:
        logger.error(f"Error in detailed readability check: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while performing detailed readability analysis"
        )


@router.post(
    "/suggestions",
    dependencies=[Depends(verify_api_key_dependency)]
)
async def readability_suggestions_endpoint(
    data: TextRequest,
    target_grade_level: Optional[int] = Query(None, ge=1, le=20)
):
    """
    Get suggestions to improve text readability.
    
    - **text**: The text to improve
    - **target_grade_level**: Desired reading grade level (1-20)
    """
    try:
        validated_text = validate_text_length(data.text)
        suggestions = await get_readability_suggestions(
            validated_text,
            target_grade_level
        )
        
        return suggestions
        
    except Exception as e:
        logger.error(f"Error getting readability suggestions: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while generating readability suggestions"
        )


@router.post(
    "/sentence-complexity",
    dependencies=[Depends(verify_api_key_dependency)]
)
async def sentence_complexity_endpoint(data: TextRequest):
    """
    Analyze sentence complexity and structure.
    
    - **text**: The text to analyze
    
    Returns complexity metrics for each sentence.
    """
    try:
        validated_text = validate_text_length(data.text)
        result = await analyze_sentence_complexity(validated_text)
        
        return result
        
    except Exception as e:
        logger.error(f"Error analyzing sentence complexity: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while analyzing sentence complexity"
        )
```

---

### **📁 routers/ai_rewrite.py**

```python
"""
AI Rewrite Router
Handles endpoints for AI-powered text rewriting and enhancement
"""
from fastapi import APIRouter, Depends, HTTPException, status, Query
from typing import Optional, List

from models.request_models import (
    TextRequest,
    RewriteRequest,
    ParaphraseRequest
)
from models.response_models import RewriteResponse, ParaphraseResponse
from models.enums import RewriteStyle, ToneType
from services.ai_rewrite_service import (
    rewrite_text,
    paraphrase_text,
    simplify_text,
    expand_text,
    rewrite_with_style
)
from dependencies import (
    verify_api_key_dependency,
    rate_limit_dependency,
    validate_text_length
)
from utils.logger import setup_logger

logger = setup_logger(__name__)
router = APIRouter()


@router.post(
    "/",
    response_model=RewriteResponse,
    dependencies=[Depends(verify_api_key_dependency), Depends(rate_limit_dependency)]
)
async def ai_rewrite_endpoint(data: RewriteRequest):
    """
    Rewrite text using AI to improve clarity and quality.
    
    - **text**: The text to rewrite
    - **style**: Rewriting style (improve, simplify, expand, formal, casual)
    - **preserve_meaning**: Whether to preserve original meaning strictly
    """
    try:
        validated_text = validate_text_length(data.text)
        result = await rewrite_text(
            validated_text,
            style=data.style,
            preserve_meaning=data.preserve_meaning
        )
        
        logger.info(f"AI rewrite completed with style: {data.style}")
        
        return result
        
    except ValueError as e:
        logger.error(f"Validation error in AI rewrite: {e}")
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail=str(e)
        )
    except Exception as e:
        logger.error(f"Error in AI rewrite: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while rewriting text"
        )


@router.post(
    "/paraphrase",
    response_model=ParaphraseResponse,
    dependencies=[Depends(verify_api_key_dependency)]
)
async def paraphrase_endpoint(data: ParaphraseRequest):
    """
    Generate paraphrased versions of the text.
    
    - **text**: The text to paraphrase
    - **num_variations**: Number of paraphrased versions to generate (1-5)
    - **creativity_level**: How creative the paraphrases should be (0.0-1.0)
    """
    try:
        validated_text = validate_text_length(data.text)
        
        if not 1 <= data.num_variations <= 5:
            raise ValueError("num_variations must be between 1 and 5")
        
        result = await paraphrase_text(
            validated_text,
            num_variations=data.num_variations,
            creativity_level=data.creativity_level
        )
        
        return result
        
    except ValueError as e:
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail=str(e)
        )
    except Exception as e:
        logger.error(f"Error in paraphrasing: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while paraphrasing text"
        )


@router.post(
    "/simplify",
    dependencies=[Depends(verify_api_key_dependency)]
)
async def simplify_endpoint(
    data: TextRequest,
    target_grade_level: int = Query(8, ge=1, le=12)
):
    """
    Simplify text to match a target reading grade level.
    
    - **text**: The text to simplify
    - **target_grade_level**: Target reading grade level (1-12)
    """
    try:
        validated_text = validate_text_length(data.text)
        result = await simplify_text(validated_text, target_grade_level)
        
        return result
        
    except Exception as e:
        logger.error(f"Error in text simplification: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while simplifying text"
        )


@router.post(
    "/expand",
    dependencies=[Depends(verify_api_key_dependency)]
)
async def expand_endpoint(
    data: TextRequest,
    expansion_factor: float = Query(1.5, ge=1.0, le=3.0)
):
    """
    Expand text by adding more detail and context.
    
    - **text**: The text to expand
    - **expansion_factor**: How much to expand (1.0 = no change, 3.0 = 3x longer)
    """
    try:
        validated_text = validate_text_length(data.text)
        result = await expand_text(validated_text, expansion_factor)
        
        return result
        
    except Exception as e:
        logger.error(f"Error in text expansion: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while expanding text"
        )


@router.post(
    "/style",
    dependencies=[Depends(verify_api_key_dependency)]
)
async def rewrite_with_style_endpoint(
    data: TextRequest,
    target_tone: ToneType = Query(..., description="Target tone for rewriting"),
    target_style: RewriteStyle = Query(..., description="Target writing style")
):
    """
    Rewrite text to match specific tone and style.
    
    - **text**: The text to rewrite
    - **target_tone**: Desired tone (formal, casual, friendly, etc.)
    - **target_style**: Desired style (professional, creative, academic, etc.)
    """
    try:
        validated_text = validate_text_length(data.text)
        result = await rewrite_with_style(
            validated_text,
            target_tone=target_tone,
            target_style=target_style
        )
        
        return result
        
    except Exception as e:
        logger.error(f"Error in style rewriting: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while rewriting with style"
        )
```

---

### **📁 routers/health.py**

```python
"""
Health Check Router
Provides endpoints for monitoring application health and status
"""
from fastapi import APIRouter, Depends
from datetime import datetime
from typing import Dict, Any

from core.database import check_db_health
from utils.cache import check_cache_health
from utils.settings import settings
from ml_models.model_loader import check_models_health

router = APIRouter()


@router.get("/")
async def health_check() -> Dict[str, Any]:
    """
    Basic health check endpoint.
    Returns simple status if application is running.
    """
    return {
        "status": "healthy",
        "timestamp": datetime.utcnow().isoformat(),
        "version": settings.VERSION
    }


@router.get("/detailed")
async def detailed_health_check() -> Dict[str, Any]:
    """
    Detailed health check endpoint.
    Checks all system components and returns their status.
    """
    health_status = {
        "status": "healthy",
        "timestamp": datetime.utcnow().isoformat(),
        "version": settings.VERSION,
        "environment": settings.ENVIRONMENT,
        "components": {}
    }
    
    # Check database
    try:
        db_healthy = await check_db_health()
        health_status["components"]["database"] = {
            "status": "healthy" if db_healthy else "unhealthy",
            "message": "Database connection successful" if db_healthy else "Database connection failed"
        }
    except Exception as e:
        health_status["components"]["database"] = {
            "status": "unhealthy",
            "message": f"Database error: {str(e)}"
        }
        health_status["status"] = "degraded"
    
    # Check cache
    try:
        cache_healthy = await check_cache_health()
        health_status["components"]["cache"] = {
            "status": "healthy" if cache_healthy else "unhealthy",
            "message": "Cache connection successful" if cache_healthy else "Cache connection failed"
        }
    except Exception as e:
        health_status["components"]["cache"] = {
            "status": "unhealthy",
            "message": f"Cache error: {str(e)}"
        }
        health_status["status"] = "degraded"
    
    # Check ML models
    try:
        models_status = await check_models_health()
        health_status["components"]["ml_models"] = models_status
        if models_status["status"] != "healthy":
            health_status["status"] = "degraded"
    except Exception as e:
        health_status["components"]["ml_models"] = {
            "status": "unhealthy",
            "message": f"ML models error: {str(e)}"
        }
        health_status["status"] = "degraded"
    
    return health_status


@router.get("/ready")
async def readiness_check() -> Dict[str, Any]:
    """
    Readiness check for Kubernetes/container orchestration.
    Returns 200 if app is ready to serve traffic.
    """
    # Check critical components
    db_ready = await check_db_health()
    
    if not db_ready:
        return {
            "ready": False,
            "message": "Application not ready - database unavailable"
        }
    
    return {
        "ready": True,
        "message": "Application ready to serve traffic"
    }


@router.get("/live")
async def liveness_check() -> Dict[str, str]:
    """
    Liveness check for Kubernetes/container orchestration.
    Always returns 200 if the application process is running.
    """
    return {
        "status": "alive",
        "timestamp": datetime.utcnow().isoformat()
    }
```

---

I'll continue with more segments. Would you like me to proceed with:
- **SEGMENT 3**: New routers (analytics, user, document) and service files?
- **SEGMENT 4**: Models (request, response, database, enums)?
- **SEGMENT 5**: Utils, Core, ML Models, and remaining files?

Let me know and I'll continue with the complete implementation!

## **SEGMENT 3: New Routers (Analytics, User, Document)**

### **📁 routers/analytics.py**

```python
"""
Analytics Router
Handles endpoints for usage analytics and statistics
"""
from fastapi import APIRouter, Depends, HTTPException, status, Query
from datetime import datetime, timedelta
from typing import Optional, List

from models.response_models import (
    AnalyticsResponse,
    UsageStatsResponse,
    TrendAnalysisResponse
)
from services.text_analysis_service import (
    get_user_analytics,
    get_usage_statistics,
    get_error_trends,
    get_improvement_metrics
)
from dependencies import (
    verify_api_key_dependency,
    get_current_active_user
)
from models.database_models import User
from utils.logger import setup_logger

logger = setup_logger(__name__)
router = APIRouter()


@router.get(
    "/usage",
    response_model=UsageStatsResponse,
    dependencies=[Depends(verify_api_key_dependency)]
)
async def get_usage_stats(
    current_user: User = Depends(get_current_active_user),
    start_date: Optional[datetime] = Query(None),
    end_date: Optional[datetime] = Query(None)
):
    """
    Get usage statistics for the current user.
    
    - **start_date**: Start date for statistics (optional)
    - **end_date**: End date for statistics (optional)
    """
    try:
        if not start_date:
            start_date = datetime.utcnow() - timedelta(days=30)
        if not end_date:
            end_date = datetime.utcnow()
        
        stats = await get_usage_statistics(
            current_user.id,
            start_date,
            end_date
        )
        
        logger.info(f"Usage stats retrieved for user {current_user.id}")
        
        return stats
        
    except Exception as e:
        logger.error(f"Error retrieving usage stats: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while retrieving usage statistics"
        )


@router.get(
    "/user-analytics",
    response_model=AnalyticsResponse,
    dependencies=[Depends(verify_api_key_dependency)]
)
async def get_analytics(
    current_user: User = Depends(get_current_active_user),
    period: str = Query("30d", regex="^(7d|30d|90d|1y)$")
):
    """
    Get comprehensive analytics for the current user.
    
    - **period**: Time period for analytics (7d, 30d, 90d, 1y)
    """
    try:
        analytics = await get_user_analytics(current_user.id, period)
        
        return analytics
        
    except Exception as e:
        logger.error(f"Error retrieving user analytics: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while retrieving analytics"
        )


@router.get(
    "/error-trends",
    response_model=TrendAnalysisResponse,
    dependencies=[Depends(verify_api_key_dependency)]
)
async def get_error_trend_analysis(
    current_user: User = Depends(get_current_active_user),
    error_type: Optional[str] = Query(None, description="Filter by error type")
):
    """
    Analyze trends in writing errors over time.
    
    - **error_type**: Optional filter for specific error types (spelling, grammar, etc.)
    """
    try:
        trends = await get_error_trends(current_user.id, error_type)
        
        return trends
        
    except Exception as e:
        logger.error(f"Error analyzing error trends: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while analyzing error trends"
        )


@router.get(
    "/improvement-metrics",
    dependencies=[Depends(verify_api_key_dependency)]
)
async def get_improvement_analysis(
    current_user: User = Depends(get_current_active_user)
):
    """
    Get metrics showing writing improvement over time.
    
    Returns analysis of how user's writing has improved.
    """
    try:
        metrics = await get_improvement_metrics(current_user.id)
        
        return metrics
        
    except Exception as e:
        logger.error(f"Error retrieving improvement metrics: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while retrieving improvement metrics"
        )


@router.post(
    "/track-event",
    dependencies=[Depends(verify_api_key_dependency)]
)
async def track_event(
    event_type: str,
    event_data: dict,
    current_user: User = Depends(get_current_active_user)
):
    """
    Track a custom analytics event.
    
    - **event_type**: Type of event to track
    - **event_data**: Additional event data
    """
    try:
        # Implementation would store event in database/analytics system
        logger.info(f"Event tracked: {event_type} for user {current_user.id}")
        
        return {
            "success": True,
            "message": "Event tracked successfully"
        }
        
    except Exception as e:
        logger.error(f"Error tracking event: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while tracking event"
        )
```

---

### **📁 routers/user.py**

```python
"""
User Router
Handles endpoints for user management and authentication
"""
from fastapi import APIRouter, Depends, HTTPException, status
from typing import List

from models.request_models import (
    UserCreateRequest,
    UserUpdateRequest,
    UserLoginRequest
)
from models.response_models import (
    UserResponse,
    UserProfileResponse,
    TokenResponse
)
from models.database_models import User
from services.suggestion_service import (
    create_user,
    update_user,
    delete_user,
    get_user_profile,
    authenticate_user,
    refresh_access_token
)
from dependencies import (
    verify_api_key_dependency,
    get_current_active_user
)
from database.repositories.user_repository import UserRepository
from utils.logger import setup_logger

logger = setup_logger(__name__)
router = APIRouter()


@router.post(
    "/register",
    response_model=UserResponse,
    status_code=status.HTTP_201_CREATED
)
async def register_user(data: UserCreateRequest):
    """
    Register a new user.
    
    - **email**: User email address
    - **password**: User password (min 8 characters)
    - **full_name**: User's full name
    """
    try:
        user = await create_user(data)
        
        logger.info(f"New user registered: {user.email}")
        
        return user
        
    except ValueError as e:
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail=str(e)
        )
    except Exception as e:
        logger.error(f"Error registering user: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred during registration"
        )


@router.post(
    "/login",
    response_model=TokenResponse
)
async def login(data: UserLoginRequest):
    """
    Authenticate user and return access token.
    
    - **email**: User email
    - **password**: User password
    """
    try:
        token_data = await authenticate_user(data.email, data.password)
        
        if not token_data:
            raise HTTPException(
                status_code=status.HTTP_401_UNAUTHORIZED,
                detail="Incorrect email or password"
            )
        
        logger.info(f"User logged in: {data.email}")
        
        return token_data
        
    except HTTPException:
        raise
    except Exception as e:
        logger.error(f"Error during login: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred during login"
        )


@router.get(
    "/me",
    response_model=UserProfileResponse,
    dependencies=[Depends(verify_api_key_dependency)]
)
async def get_current_user_profile(
    current_user: User = Depends(get_current_active_user)
):
    """
    Get current user's profile information.
    """
    try:
        profile = await get_user_profile(current_user.id)
        
        return profile
        
    except Exception as e:
        logger.error(f"Error retrieving user profile: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while retrieving profile"
        )


@router.put(
    "/me",
    response_model=UserResponse,
    dependencies=[Depends(verify_api_key_dependency)]
)
async def update_current_user(
    data: UserUpdateRequest,
    current_user: User = Depends(get_current_active_user)
):
    """
    Update current user's profile information.
    
    - **full_name**: Updated full name
    - **email**: Updated email address
    - **preferences**: User preferences
    """
    try:
        updated_user = await update_user(current_user.id, data)
        
        logger.info(f"User updated: {current_user.id}")
        
        return updated_user
        
    except ValueError as e:
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail=str(e)
        )
    except Exception as e:
        logger.error(f"Error updating user: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while updating profile"
        )


@router.delete(
    "/me",
    status_code=status.HTTP_204_NO_CONTENT,
    dependencies=[Depends(verify_api_key_dependency)]
)
async def delete_current_user(
    current_user: User = Depends(get_current_active_user)
):
    """
    Delete current user's account.
    """
    try:
        await delete_user(current_user.id)
        
        logger.info(f"User deleted: {current_user.id}")
        
        return None
        
    except Exception as e:
        logger.error(f"Error deleting user: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while deleting account"
        )


@router.post(
    "/refresh-token",
    response_model=TokenResponse
)
async def refresh_token(refresh_token: str):
    """
    Refresh access token using refresh token.
    
    - **refresh_token**: Valid refresh token
    """
    try:
        token_data = await refresh_access_token(refresh_token)
        
        if not token_data:
            raise HTTPException(
                status_code=status.HTTP_401_UNAUTHORIZED,
                detail="Invalid refresh token"
            )
        
        return token_data
        
    except HTTPException:
        raise
    except Exception as e:
        logger.error(f"Error refreshing token: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while refreshing token"
        )


@router.get(
    "/preferences",
    dependencies=[Depends(verify_api_key_dependency)]
)
async def get_user_preferences(
    current_user: User = Depends(get_current_active_user)
):
    """
    Get user's preferences and settings.
    """
    try:
        return current_user.preferences or {}
        
    except Exception as e:
        logger.error(f"Error retrieving preferences: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while retrieving preferences"
        )


@router.put(
    "/preferences",
    dependencies=[Depends(verify_api_key_dependency)]
)
async def update_user_preferences(
    preferences: dict,
    current_user: User = Depends(get_current_active_user)
):
    """
    Update user's preferences and settings.
    
    - **preferences**: Dictionary of user preferences
    """
    try:
        # Update preferences in database
        updated_user = await update_user(
            current_user.id,
            UserUpdateRequest(preferences=preferences)
        )
        
        logger.info(f"Preferences updated for user: {current_user.id}")
        
        return {
            "success": True,
            "preferences": updated_user.preferences
        }
        
    except Exception as e:
        logger.error(f"Error updating preferences: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while updating preferences"
        )
```

---

### **📁 routers/document.py**

```python
"""
Document Router
Handles endpoints for document management and storage
"""
from fastapi import (
    APIRouter,
    Depends,
    HTTPException,
    status,
    UploadFile,
    File,
    Query
)
from typing import List, Optional
from datetime import datetime

from models.request_models import DocumentCreateRequest, DocumentUpdateRequest
from models.response_models import DocumentResponse, DocumentListResponse
from models.database_models import User
from services.context_service import (
    create_document,
    get_document,
    update_document,
    delete_document,
    list_user_documents,
    process_uploaded_file
)
from dependencies import (
    verify_api_key_dependency,
    get_current_active_user,
    CommonQueryParams
)
from utils.logger import setup_logger
from utils.settings import settings

logger = setup_logger(__name__)
router = APIRouter()


@router.post(
    "/",
    response_model=DocumentResponse,
    status_code=status.HTTP_201_CREATED,
    dependencies=[Depends(verify_api_key_dependency)]
)
async def create_new_document(
    data: DocumentCreateRequest,
    current_user: User = Depends(get_current_active_user)
):
    """
    Create a new document.
    
    - **title**: Document title
    - **content**: Document content
    - **tags**: Optional tags for categorization
    """
    try:
        document = await create_document(current_user.id, data)
        
        logger.info(f"Document created: {document.id} by user {current_user.id}")
        
        return document
        
    except ValueError as e:
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail=str(e)
        )
    except Exception as e:
        logger.error(f"Error creating document: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while creating document"
        )


@router.post(
    "/upload",
    response_model=DocumentResponse,
    dependencies=[Depends(verify_api_key_dependency)]
)
async def upload_document(
    file: UploadFile = File(...),
    current_user: User = Depends(get_current_active_user)
):
    """
    Upload a document file.
    
    Supported formats: .txt, .doc, .docx, .pdf
    """
    try:
        # Check file extension
        if not any(file.filename.endswith(ext) for ext in settings.ALLOWED_EXTENSIONS):
            raise ValueError(
                f"Unsupported file type. Allowed: {', '.join(settings.ALLOWED_EXTENSIONS)}"
            )
        
        # Check file size
        content = await file.read()
        if len(content) > settings.MAX_UPLOAD_SIZE:
            raise ValueError(
                f"File too large. Maximum size: {settings.MAX_UPLOAD_SIZE / 1024 / 1024}MB"
            )
        
        # Process file
        document = await process_uploaded_file(
            current_user.id,
            file.filename,
            content
        )
        
        logger.info(f"File uploaded: {file.filename} by user {current_user.id}")
        
        return document
        
    except ValueError as e:
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail=str(e)
        )
    except Exception as e:
        logger.error(f"Error uploading file: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while uploading file"
        )


@router.get(
    "/",
    response_model=DocumentListResponse,
    dependencies=[Depends(verify_api_key_dependency)]
)
async def list_documents(
    current_user: User = Depends(get_current_active_user),
    commons: CommonQueryParams = Depends(),
    search: Optional[str] = Query(None, description="Search in title and content"),
    tags: Optional[List[str]] = Query(None, description="Filter by tags")
):
    """
    List all documents for the current user.
    
    - **skip**: Number of documents to skip (pagination)
    - **limit**: Maximum number of documents to return
    - **search**: Search query for title/content
    - **tags**: Filter by tags
    """
    try:
        documents = await list_user_documents(
            current_user.id,
            skip=commons.skip,
            limit=commons.limit,
            search=search,
            tags=tags
        )
        
        return documents
        
    except Exception as e:
        logger.error(f"Error listing documents: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while listing documents"
        )


@router.get(
    "/{document_id}",
    response_model=DocumentResponse,
    dependencies=[Depends(verify_api_key_dependency)]
)
async def get_document_by_id(
    document_id: str,
    current_user: User = Depends(get_current_active_user)
):
    """
    Get a specific document by ID.
    
    - **document_id**: The document ID
    """
    try:
        document = await get_document(document_id, current_user.id)
        
        if not document:
            raise HTTPException(
                status_code=status.HTTP_404_NOT_FOUND,
                detail="Document not found"
            )
        
        return document
        
    except HTTPException:
        raise
    except Exception as e:
        logger.error(f"Error retrieving document: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while retrieving document"
        )


@router.put(
    "/{document_id}",
    response_model=DocumentResponse,
    dependencies=[Depends(verify_api_key_dependency)]
)
async def update_document_by_id(
    document_id: str,
    data: DocumentUpdateRequest,
    current_user: User = Depends(get_current_active_user)
):
    """
    Update a document.
    
    - **document_id**: The document ID
    - **title**: Updated title
    - **content**: Updated content
    - **tags**: Updated tags
    """
    try:
        document = await update_document(document_id, current_user.id, data)
        
        if not document:
            raise HTTPException(
                status_code=status.HTTP_404_NOT_FOUND,
                detail="Document not found"
            )
        
        logger.info(f"Document updated: {document_id} by user {current_user.id}")
        
        return document
        
    except HTTPException:
        raise
    except ValueError as e:
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail=str(e)
        )
    except Exception as e:
        logger.error(f"Error updating document: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while updating document"
        )


@router.delete(
    "/{document_id}",
    status_code=status.HTTP_204_NO_CONTENT,
    dependencies=[Depends(verify_api_key_dependency)]
)
async def delete_document_by_id(
    document_id: str,
    current_user: User = Depends(get_current_active_user)
):
    """
    Delete a document.
    
    - **document_id**: The document ID
    """
    try:
        success = await delete_document(document_id, current_user.id)
        
        if not success:
            raise HTTPException(
                status_code=status.HTTP_404_NOT_FOUND,
                detail="Document not found"
            )
        
        logger.info(f"Document deleted: {document_id} by user {current_user.id}")
        
        return None
        
    except HTTPException:
        raise
    except Exception as e:
        logger.error(f"Error deleting document: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while deleting document"
        )


@router.post(
    "/{document_id}/analyze",
    dependencies=[Depends(verify_api_key_dependency)]
)
async def analyze_document(
    document_id: str,
    current_user: User = Depends(get_current_active_user),
    full_analysis: bool = Query(True, description="Perform full analysis")
):
    """
    Perform comprehensive analysis on a document.
    
    Runs spelling, grammar, tone, and readability checks.
    
    - **document_id**: The document ID
    - **full_analysis**: Whether to perform full analysis
    """
    try:
        document = await get_document(document_id, current_user.id)
        
        if not document:
            raise HTTPException(
                status_code=status.HTTP_404_NOT_FOUND,
                detail="Document not found"
            )
        
        # Perform analysis (this would call multiple services)
        from services.text_analysis_service import analyze_document_full
        
        analysis_result = await analyze_document_full(
            document.content,
            full_analysis=full_analysis
        )
        
        logger.info(f"Document analyzed: {document_id}")
        
        return analysis_result
        
    except HTTPException:
        raise
    except Exception as e:
        logger.error(f"Error analyzing document: {e}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="An error occurred while analyzing document"
        )
```

---

## **SEGMENT 4: Service Files (Updated + New)**

### **📁 services/__init__.py**

```python
"""
Services package - Business logic layer
All service functions are imported here
"""
from . import (
    spelling_service,
    grammar_service,
    tone_service,
    readability_service,
    ai_rewrite_service,
    text_analysis_service,
    suggestion_service,
    context_service,
    plagiarism_service,
    style_service
)

__all__ = [
    "spelling_service",
    "grammar_service",
    "tone_service",
    "readability_service",
    "ai_rewrite_service",
    "text_analysis_service",
    "suggestion_service",
    "context_service",
    "plagiarism_service",
    "style_service"
]
```

---

### **📁 services/spelling_service.py**

```python
"""
Spelling Service
Handles spelling check logic and corrections
"""
from typing import List, Dict, Optional
import asyncio
from spellchecker import SpellChecker

from models.response_models import (
    SpellingCheckResponse,
    SpellingIssue,
    BatchSpellingCheckResponse
)
from utils.cache import cache_result, get_cached_result
from utils.text_processing import tokenize_words, clean_text
from utils.logger import setup_logger
from ml_models.spell_checker import load_spell_checker

logger = setup_logger(__name__)

# Global spell checker instance (lazy loaded)
_spell_checker: Optional[SpellChecker] = None


def get_spell_checker() -> SpellChecker:
    """Get or initialize spell checker"""
    global _spell_checker
    if _spell_checker is None:
        _spell_checker = load_spell_checker()
    return _spell_checker


async def check_spelling(text: str) -> SpellingCheckResponse:
    """
    Check spelling in the provided text.
    
    Args:
        text: Text to check
        
    Returns:
        SpellingCheckResponse with list of issues and suggestions
    """
    try:
        # Check cache first
        cache_key = f"spelling:{hash(text)}"
        cached_result = await get_cached_result(cache_key)
        if cached_result:
            logger.debug("Returning cached spelling result")
            return SpellingCheckResponse(**cached_result)
        
        # Clean and tokenize text
        cleaned_text = clean_text(text)
        words = tokenize_words(cleaned_text)
        
        # Get spell checker
        spell_checker = get_spell_checker()
        
        # Find misspelled words
        misspelled = spell_checker.unknown(words)
        
        issues: List[SpellingIssue] = []
        
        for word in misspelled:
            # Get suggestions for each misspelled word
            suggestions = spell_checker.candidates(word)
            
            if suggestions:
                # Find word position in original text
                start_pos = text.lower().find(word.lower())
                
                issue = SpellingIssue(
                    word=word,
                    position=start_pos,
                    suggestions=list(suggestions)[:5],  # Top 5 suggestions
                    confidence=0.8,  # Placeholder confidence score
                    context=_get_word_context(text, start_pos, len(word))
                )
                issues.append(issue)
        
        response = SpellingCheckResponse(
            text=text,
            issues=issues,
            total_issues=len(issues),
            corrected_count=0
        )
        
        # Cache result
        await cache_result(cache_key, response.dict(), ttl=3600)
        
        logger.info(f"Spelling check completed: {len(issues)} issues found")
        
        return response
        
    except Exception as e:
        logger.error(f"Error in spelling check: {e}")
        raise


async def get_spelling_suggestions(
    word: str,
    max_suggestions: int = 5
) -> List[str]:
    """
    Get spelling suggestions for a specific word.
    
    Args:
        word: Word to get suggestions for
        max_suggestions: Maximum number of suggestions
        
    Returns:
        List of suggested corrections
    """
    try:
        spell_checker = get_spell_checker()
        suggestions = spell_checker.candidates(word)
        
        if not suggestions:
            return []
        
        return list(suggestions)[:max_suggestions]
        
    except Exception as e:
        logger.error(f"Error getting spelling suggestions: {e}")
        return []


async def check_spelling_batch(
    texts: List[str]
) -> BatchSpellingCheckResponse:
    """
    Check spelling for multiple texts.
    
    Args:
        texts: List of texts to check
        
    Returns:
        BatchSpellingCheckResponse with results for each text
    """
    try:
        # Process texts concurrently
        tasks = [check_spelling(text) for text in texts]
        results = await asyncio.gather(*tasks, return_exceptions=True)
        
        # Filter out exceptions
        valid_results = [
            result for result in results
            if isinstance(result, SpellingCheckResponse)
        ]
        
        return BatchSpellingCheckResponse(
            results=valid_results,
            total_texts=len(texts),
            processed_texts=len(valid_results)
        )
        
    except Exception as e:
        logger.error(f"Error in batch spelling check: {e}")
        raise


def _get_word_context(text: str, position: int, word_length: int) -> str:
    """
    Extract context around a word for better understanding.
    
    Args:
        text: Full text
        position: Starting position of word
        word_length: Length of the word
        
    Returns:
        Context string
    """
    context_range = 30
    start = max(0, position - context_range)
    end = min(len(text), position + word_length + context_range)
    
    context = text[start:end]
    
    # Add ellipsis if context was truncated
    if start > 0:
        context = "..." + context
    if end < len(text):
        context = context + "..."
    
    return context
```

---

### **📁 services/grammar_service.py**

```python
"""
Grammar Service
Handles grammar checking and correction logic
"""
from typing import List, Dict, Optional
import language_tool_python

from models.response_models import (
    GrammarCheckResponse,
    GrammarIssue,
    GrammarCorrectionResponse
)
from models.enums import GrammarRuleType
from utils.cache import cache_result, get_cached_result
from utils.logger import setup_logger
from ml_models.grammar_model import load_grammar_model

logger = setup_logger(__name__)

# Global grammar tool instance
_grammar_tool: Optional[language_tool_python.LanguageTool] = None


def get_grammar_tool() -> language_tool_python.LanguageTool:
    """Get or initialize grammar checking tool"""
    global _grammar_tool
    if _grammar_tool is None:
        _grammar_tool = language_tool_python.LanguageTool('en-US')
    return _grammar_tool


async def check_grammar(text: str) -> GrammarCheckResponse:
    """
    Check grammar in the provided text.
    
    Args:
        text: Text to check
        
    Returns:
        GrammarCheckResponse with list of grammar issues
    """
    try:
        # Check cache
        cache_key = f"grammar:{hash(text)}"
        cached_result = await get_cached_result(cache_key)
        if cached_result:
            logger.debug("Returning cached grammar result")
            return GrammarCheckResponse(**cached_result)
        
        # Get grammar tool
        tool = get_grammar_tool()
        
        # Check grammar
        matches = tool.check(text)
        
        issues: List[GrammarIssue] = []
        
        for match in matches:
            issue = GrammarIssue(
                rule_id=match.ruleId,
                rule_description=match.message,
                error_text=match.matchedText,
                position=match.offset,
                length=match.errorLength,
                suggestions=[r for r in match.replacements[:3]],  # Top 3 suggestions
                category=_map_category_to_rule_type(match.category),
                severity=_determine_severity(match),
                context=match.context
            )
            issues.append(issue)
        
        response = GrammarCheckResponse(
            text=text,
            issues=issues,
            total_issues=len(issues),
            critical_issues=sum(1 for i in issues if i.severity == "high")
        )
        
        # Cache result
        await cache_result(cache_key, response.dict(), tt


=3600)
        
        logger.info(f"Grammar check completed: {len(issues)} issues found")
        
        return response
        
    except Exception as e:
        logger.error(f"Error in grammar check: {e}")
        raise


async def check_grammar_detailed(
    text: str,
    rule_types: Optional[List[GrammarRuleType]] = None,
    include_explanations: bool = True
) -> Dict:
    """
    Perform detailed grammar check with explanations.
    
    Args:
        text: Text to check
        rule_types: Optional specific rule types to check
        include_explanations: Whether to include detailed explanations
        
    Returns:
        Detailed grammar analysis
    """
    try:
        tool = get_grammar_tool()
        matches = tool.check(text)
        
        detailed_issues = []
        
        for match in matches:
            rule_type = _map_category_to_rule_type(match.category)
            
            # Filter by rule types if specified
            if rule_types and rule_type not in rule_types:
                continue
            
            issue_detail = {
                "rule_id": match.ruleId,
                "description": match.message,
                "error_text": match.matchedText,
                "position": match.offset,
                "suggestions": [r for r in match.replacements[:5]],
                "category": rule_type,
                "severity": _determine_severity(match),
                "context": match.context
            }
            
            if include_explanations:
                issue_detail["explanation"] = _get_rule_explanation(match.ruleId)
            
            detailed_issues.append(issue_detail)
        
        return {
            "text": text,
            "issues": detailed_issues,
            "summary": {
                "total": len(detailed_issues),
                "by_category": _group_by_category(detailed_issues),
                "by_severity": _group_by_severity(detailed_issues)
            }
        }
        
    except Exception as e:
        logger.error(f"Error in detailed grammar check: {e}")
        raise


async def apply_grammar_correction(
    text: str,
    auto_apply: bool = True
) -> GrammarCorrectionResponse:
    """
    Apply grammar corrections to text.
    
    Args:
        text: Text to correct
        auto_apply: Whether to automatically apply all corrections
        
    Returns:
        GrammarCorrectionResponse with corrected text
    """
    try:
        tool = get_grammar_tool()
        
        if auto_apply:
            # Apply all corrections automatically
            corrected_text = tool.correct(text)
            
            return GrammarCorrectionResponse(
                original_text=text,
                corrected_text=corrected_text,
                corrections_applied=True,
                changes_made=_count_changes(text, corrected_text)
            )
        else:
            # Return suggestions without applying
            matches = tool.check(text)
            
            return GrammarCorrectionResponse(
                original_text=text,
                corrected_text=text,
                corrections_applied=False,
                suggestions=[
                    {
                        "position": m.offset,
                        "original": m.matchedText,
                        "suggestions": [r for r in m.replacements[:3]]
                    }
                    for m in matches
                ]
            )
        
    except Exception as e:
        logger.error(f"Error applying grammar correction: {e}")
        raise


def _map_category_to_rule_type(category: str) -> GrammarRuleType:
    """Map LanguageTool category to our GrammarRuleType enum"""
    category_map = {
        "GRAMMAR": GrammarRuleType.GRAMMAR,
        "TYPOS": GrammarRuleType.SPELLING,
        "PUNCTUATION": GrammarRuleType.PUNCTUATION,
        "CASING": GrammarRuleType.CAPITALIZATION,
        "STYLE": GrammarRuleType.STYLE,
        "CONFUSED_WORDS": GrammarRuleType.WORD_CHOICE
    }
    return category_map.get(category, GrammarRuleType.OTHER)


def _determine_severity(match) -> str:
    """Determine severity of grammar issue"""
    if "error" in match.message.lower():
        return "high"
    elif "style" in match.category.lower():
        return "low"
    else:
        return "medium"


def _get_rule_explanation(rule_id: str) -> str:
    """Get detailed explanation for a grammar rule"""
    # This would fetch from a database or knowledge base
    explanations = {
        "MORFOLOGIK_RULE_EN_US": "This appears to be a spelling error.",
        "UPPERCASE_SENTENCE_START": "Sentences should start with a capital letter.",
        # Add more explanations as needed
    }
    return explanations.get(rule_id, "No detailed explanation available.")


def _group_by_category(issues: List[Dict]) -> Dict:
    """Group issues by category"""
    categories = {}
    for issue in issues:
        cat = issue["category"]
        categories[cat] = categories.get(cat, 0) + 1
    return categories


def _group_by_severity(issues: List[Dict]) -> Dict:
    """Group issues by severity"""
    severities = {}
    for issue in issues:
        sev = issue["severity"]
        severities[sev] = severities.get(sev, 0) + 1
    return severities


def _count_changes(original: str, corrected: str) -> int:
    """Count number of changes between original and corrected text"""
    # Simple word-based comparison
    orig_words = original.split()
    corr_words = corrected.split()
    
    changes = 0
    for i, (o, c) in enumerate(zip(orig_words, corr_words)):
        if o != c:
            changes += 1
    
    changes += abs(len(orig_words) - len(corr_words))
    
    return changes
```

---

Let me continue with more service files in the next segment. Would you like me to proceed?

## **SEGMENT 5: Service Files (Continued)**

### **📁 services/tone_service.py**

```python
"""
Tone Service
Handles tone analysis and detection logic
"""
from typing import List, Dict, Optional
from transformers import pipeline
import numpy as np

from models.response_models import (
    ToneAnalysisResponse,
    ToneSuggestionResponse,
    SentimentScore
)
from models.enums import ToneType
from utils.cache import cache_result, get_cached_result
from utils.text_processing import split_into_sentences
from utils.logger import setup_logger
from ml_models.tone_model import load_tone_model

logger = setup_logger(__name__)

# Global tone analyzer instance
_tone_analyzer = None


def get_tone_analyzer():
    """Get or initialize tone analyzer"""
    global _tone_analyzer
    if _tone_analyzer is None:
        _tone_analyzer = load_tone_model()
    return _tone_analyzer


async def analyze_tone(text: str) -> ToneAnalysisResponse:
    """
    Analyze the tone of the provided text.
    
    Args:
        text: Text to analyze
        
    Returns:
        ToneAnalysisResponse with tone classification
    """
    try:
        # Check cache
        cache_key = f"tone:{hash(text)}"
        cached_result = await get_cached_result(cache_key)
        if cached_result:
            logger.debug("Returning cached tone result")
            return ToneAnalysisResponse(**cached_result)
        
        # Get tone analyzer
        analyzer = get_tone_analyzer()
        
        # Analyze overall tone
        result = analyzer(text[:512])  # Limit to 512 chars for model
        
        # Process results
        tone_scores = {}
        for item in result:
            label = item['label'].lower()
            score = item['score']
            tone_scores[label] = score
        
        # Map to our tone types
        primary_tone = _determine_primary_tone(tone_scores, text)
        
        # Analyze sentiment
        sentiment = _analyze_sentiment(text, analyzer)
        
        response = ToneAnalysisResponse(
            text=text,
            primary_tone=primary_tone,
            tone_scores=tone_scores,
            sentiment=sentiment,
            confidence=max(tone_scores.values()) if tone_scores else 0.0,
            formality_score=_calculate_formality(text)
        )
        
        # Cache result
        await cache_result(cache_key, response.dict(), ttl=3600)
        
        logger.info(f"Tone analysis completed: {primary_tone}")
        
        return response
        
    except Exception as e:
        logger.error(f"Error in tone analysis: {e}")
        raise


async def analyze_tone_detailed(
    text: str,
    target_tone: Optional[ToneType] = None,
    include_sentiment: bool = True
) -> Dict:
    """
    Perform detailed tone analysis with sentence-level breakdown.
    
    Args:
        text: Text to analyze
        target_tone: Optional target tone to compare against
        include_sentiment: Whether to include sentiment analysis
        
    Returns:
        Detailed tone analysis
    """
    try:
        analyzer = get_tone_analyzer()
        
        # Split into sentences
        sentences = split_into_sentences(text)
        
        sentence_analyses = []
        
        for i, sentence in enumerate(sentences):
            if len(sentence.strip()) < 5:
                continue
            
            # Analyze each sentence
            result = analyzer(sentence[:512])
            
            tone_scores = {item['label'].lower(): item['score'] for item in result}
            primary_tone = _determine_primary_tone(tone_scores, sentence)
            
            analysis = {
                "sentence": sentence,
                "position": i,
                "primary_tone": primary_tone,
                "tone_scores": tone_scores,
                "confidence": max(tone_scores.values()) if tone_scores else 0.0
            }
            
            if include_sentiment:
                analysis["sentiment"] = _analyze_sentiment(sentence, analyzer)
            
            sentence_analyses.append(analysis)
        
        # Overall analysis
        overall_result = {
            "text": text,
            "sentence_count": len(sentences),
            "sentences": sentence_analyses,
            "overall_tone": _aggregate_tones([s["primary_tone"] for s in sentence_analyses]),
            "tone_consistency": _calculate_tone_consistency(sentence_analyses)
        }
        
        if target_tone:
            overall_result["target_alignment"] = _calculate_tone_alignment(
                overall_result["overall_tone"],
                target_tone
            )
        
        return overall_result
        
    except Exception as e:
        logger.error(f"Error in detailed tone analysis: {e}")
        raise


async def get_tone_suggestions(
    text: str,
    target_tone: ToneType
) -> ToneSuggestionResponse:
    """
    Get suggestions to adjust text to match target tone.
    
    Args:
        text: Original text
        target_tone: Desired tone
        
    Returns:
        ToneSuggestionResponse with suggestions
    """
    try:
        # Analyze current tone
        current_analysis = await analyze_tone(text)
        
        # Generate suggestions based on target tone
        suggestions = _generate_tone_suggestions(
            text,
            current_analysis.primary_tone,
            target_tone
        )
        
        # Generate rewritten examples
        examples = _generate_tone_examples(text, target_tone)
        
        return ToneSuggestionResponse(
            original_text=text,
            current_tone=current_analysis.primary_tone,
            target_tone=target_tone,
            suggestions=suggestions,
            examples=examples,
            estimated_changes=len(suggestions)
        )
        
    except Exception as e:
        logger.error(f"Error generating tone suggestions: {e}")
        raise


async def compare_tones(
    original_text: str,
    modified_text: str
) -> Dict:
    """
    Compare tone between two texts.
    
    Args:
        original_text: Original text
        modified_text: Modified text
        
    Returns:
        Comparison results
    """
    try:
        # Analyze both texts
        original_analysis = await analyze_tone(original_text)
        modified_analysis = await analyze_tone(modified_text)
        
        # Calculate differences
        tone_shift = _calculate_tone_shift(
            original_analysis.tone_scores,
            modified_analysis.tone_scores
        )
        
        return {
            "original": {
                "text": original_text,
                "tone": original_analysis.primary_tone,
                "scores": original_analysis.tone_scores
            },
            "modified": {
                "text": modified_text,
                "tone": modified_analysis.primary_tone,
                "scores": modified_analysis.tone_scores
            },
            "comparison": {
                "tone_changed": original_analysis.primary_tone != modified_analysis.primary_tone,
                "tone_shift": tone_shift,
                "improvement_score": _calculate_improvement_score(
                    original_analysis,
                    modified_analysis
                )
            }
        }
        
    except Exception as e:
        logger.error(f"Error comparing tones: {e}")
        raise


def _determine_primary_tone(tone_scores: Dict, text: str) -> ToneType:
    """Determine primary tone from scores and text analysis"""
    if not tone_scores:
        return ToneType.NEUTRAL
    
    # Get highest scoring tone
    max_tone = max(tone_scores, key=tone_scores.get)
    
    # Map to our ToneType enum
    tone_map = {
        "positive": ToneType.FRIENDLY,
        "negative": ToneType.CRITICAL,
        "neutral": ToneType.NEUTRAL,
        "joy": ToneType.ENTHUSIASTIC,
        "sadness": ToneType.EMPATHETIC,
        "anger": ToneType.ASSERTIVE,
        "fear": ToneType.CAUTIOUS
    }
    
    # Additional text-based analysis
    formal_indicators = ["therefore", "consequently", "furthermore", "moreover"]
    casual_indicators = ["yeah", "gonna", "wanna", "kinda"]
    
    text_lower = text.lower()
    
    if any(indicator in text_lower for indicator in formal_indicators):
        return ToneType.FORMAL
    elif any(indicator in text_lower for indicator in casual_indicators):
        return ToneType.CASUAL
    
    return tone_map.get(max_tone, ToneType.NEUTRAL)


def _analyze_sentiment(text: str, analyzer) -> SentimentScore:
    """Analyze sentiment of text"""
    try:
        result = analyzer(text[:512])
        
        sentiment_map = {
            "positive": 1.0,
            "negative": -1.0,
            "neutral": 0.0
        }
        
        score = 0.0
        for item in result:
            label = item['label'].lower()
            if label in sentiment_map:
                score = sentiment_map[label] * item['score']
                break
        
        return SentimentScore(
            score=score,
            magnitude=abs(score),
            label="positive" if score > 0.1 else "negative" if score < -0.1 else "neutral"
        )
        
    except Exception as e:
        logger.error(f"Error analyzing sentiment: {e}")
        return SentimentScore(score=0.0, magnitude=0.0, label="neutral")


def _calculate_formality(text: str) -> float:
    """Calculate formality score (0.0 = casual, 1.0 = formal)"""
    formal_markers = [
        "therefore", "consequently", "furthermore", "moreover",
        "nevertheless", "accordingly", "hence", "thus"
    ]
    casual_markers = [
        "yeah", "gonna", "wanna", "kinda", "sorta",
        "cool", "awesome", "stuff", "things"
    ]
    
    text_lower = text.lower()
    words = text_lower.split()
    
    formal_count = sum(1 for marker in formal_markers if marker in text_lower)
    casual_count = sum(1 for marker in casual_markers if marker in text_lower)
    
    # Calculate score
    if formal_count + casual_count == 0:
        return 0.5  # Neutral
    
    score = formal_count / (formal_count + casual_count)
    
    return score


def _aggregate_tones(tones: List[ToneType]) -> ToneType:
    """Aggregate multiple tones into overall tone"""
    if not tones:
        return ToneType.NEUTRAL
    
    # Count occurrences
    tone_counts = {}
    for tone in tones:
        tone_counts[tone] = tone_counts.get(tone, 0) + 1
    
    # Return most common tone
    return max(tone_counts, key=tone_counts.get)


def _calculate_tone_consistency(sentence_analyses: List[Dict]) -> float:
    """Calculate how consistent the tone is across sentences"""
    if len(sentence_analyses) <= 1:
        return 1.0
    
    tones = [s["primary_tone"] for s in sentence_analyses]
    unique_tones = len(set(tones))
    
    # Lower score = more variation
    consistency = 1.0 - (unique_tones - 1) / len(tones)
    
    return max(0.0, min(1.0, consistency))


def _calculate_tone_alignment(current_tone: ToneType, target_tone: ToneType) -> Dict:
    """Calculate alignment between current and target tone"""
    aligned = current_tone == target_tone
    
    # Define tone similarity matrix
    similarity_scores = {
        (ToneType.FORMAL, ToneType.PROFESSIONAL): 0.8,
        (ToneType.CASUAL, ToneType.FRIENDLY): 0.8,
        (ToneType.CONFIDENT, ToneType.ASSERTIVE): 0.7,
        # Add more similarities as needed
    }
    
    similarity = similarity_scores.get((current_tone, target_tone), 0.0)
    
    return {
        "aligned": aligned,
        "similarity": similarity,
        "recommendation": "maintain" if aligned else "adjust"
    }


def _generate_tone_suggestions(
    text: str,
    current_tone: ToneType,
    target_tone: ToneType
) -> List[Dict]:
    """Generate suggestions to adjust tone"""
    suggestions = []
    
    # Define tone adjustment strategies
    if target_tone == ToneType.FORMAL and current_tone == ToneType.CASUAL:
        suggestions.append({
            "type": "word_choice",
            "suggestion": "Replace casual words with formal alternatives",
            "examples": ["gonna → going to", "wanna → want to"]
        })
        suggestions.append({
            "type": "structure",
            "suggestion": "Use complete sentences and avoid contractions",
            "examples": ["can't → cannot", "it's → it is"]
        })
    
    elif target_tone == ToneType.CASUAL and current_tone == ToneType.FORMAL:
        suggestions.append({
            "type": "word_choice",
            "suggestion": "Use simpler, conversational language",
            "examples": ["utilize → use", "commence → start"]
        })
        suggestions.append({
            "type": "structure",
            "suggestion": "Use contractions and shorter sentences",
            "examples": ["it is → it's", "do not → don't"]
        })
    
    elif target_tone == ToneType.FRIENDLY:
        suggestions.append({
            "type": "tone",
            "suggestion": "Add warm, welcoming language",
            "examples": ["Add greetings", "Use 'we' instead of 'I'"]
        })
    
    return suggestions


def _generate_tone_examples(text: str, target_tone: ToneType) -> List[str]:
    """Generate example rewrites for target tone"""
    # This would use AI rewriting in a real implementation
    examples = []
    
    if target_tone == ToneType.FORMAL:
        examples.append(f"Formal version: {text[:100]}...")
    elif target_tone == ToneType.CASUAL:
        examples.append(f"Casual version: {text[:100]}...")
    
    return examples


def _calculate_tone_shift(
    original_scores: Dict,
    modified_scores: Dict
) -> Dict:
    """Calculate shift in tone scores"""
    shifts = {}
    
    all_tones = set(original_scores.keys()) | set(modified_scores.keys())
    
    for tone in all_tones:
        orig = original_scores.get(tone, 0.0)
        mod = modified_scores.get(tone, 0.0)
        shifts[tone] = mod - orig
    
    return shifts


def _calculate_improvement_score(
    original_analysis: ToneAnalysisResponse,
    modified_analysis: ToneAnalysisResponse
) -> float:
    """Calculate overall improvement score"""
    # Simple heuristic: higher confidence = better
    confidence_improvement = modified_analysis.confidence - original_analysis.confidence
    
    # Normalize to 0-1 scale
    improvement = (confidence_improvement + 1) / 2
    
    return max(0.0, min(1.0, improvement))
```

---

### **📁 services/readability_service.py**

```python
"""
Readability Service
Handles readability analysis and scoring logic
"""
from typing import List, Dict, Optional
import textstat
import re

from models.response_models import (
    ReadabilityResponse,
    ReadabilityDetailedResponse,
    ReadabilityMetrics,
    SentenceComplexity
)
from utils.cache import cache_result, get_cached_result
from utils.text_processing import split_into_sentences, count_syllables
from utils.logger import setup_logger

logger = setup_logger(__name__)


async def check_readability(text: str) -> ReadabilityResponse:
    """
    Calculate readability scores for the provided text.
    
    Args:
        text: Text to analyze
        
    Returns:
        ReadabilityResponse with multiple readability metrics
    """
    try:
        # Check cache
        cache_key = f"readability:{hash(text)}"
        cached_result = await get_cached_result(cache_key)
        if cached_result:
            logger.debug("Returning cached readability result")
            return ReadabilityResponse(**cached_result)
        
        # Calculate various readability metrics
        metrics = ReadabilityMetrics(
            flesch_reading_ease=textstat.flesch_reading_ease(text),
            flesch_kincaid_grade=textstat.flesch_kincaid_grade(text),
            gunning_fog=textstat.gunning_fog(text),
            smog_index=textstat.smog_index(text),
            coleman_liau_index=textstat.coleman_liau_index(text),
            automated_readability_index=textstat.automated_readability_index(text),
            dale_chall_readability=textstat.dale_chall_readability_score(text)
        )
        
        # Calculate additional statistics
        sentences = split_into_sentences(text)
        words = text.split()
        
        response = ReadabilityResponse(
            text=text,
            metrics=metrics,
            grade_level=_calculate_average_grade_level(metrics),
            reading_time_minutes=_estimate_reading_time(text),
            difficulty_level=_determine_difficulty_level(metrics.flesch_reading_ease),
            word_count=len(words),
            sentence_count=len(sentences),
            avg_words_per_sentence=len(words) / len(sentences) if sentences else 0,
            recommendations=_generate_readability_recommendations(metrics, text)
        )
        
        # Cache result
        await cache_result(cache_key, response.dict(), ttl=3600)
        
        logger.info(f"Readability check completed: Grade level {response.grade_level}")
        
        return response
        
    except Exception as e:
        logger.error(f"Error in readability check: {e}")
        raise


async def check_readability_detailed(
    text: str,
    target_grade_level: Optional[int] = None
) -> ReadabilityDetailedResponse:
    """
    Perform detailed readability analysis with sentence-level metrics.
    
    Args:
        text: Text to analyze
        target_grade_level: Optional target reading grade level
        
    Returns:
        ReadabilityDetailedResponse with detailed breakdown
    """
    try:
        # Get basic readability
        basic_response = await check_readability(text)
        
        # Analyze each sentence
        sentences = split_into_sentences(text)
        sentence_analyses = []
        
        for i, sentence in enumerate(sentences):
            if len(sentence.strip()) < 3:
                continue
            
            complexity = _analyze_sentence_structure(sentence)
            
            sentence_analyses.append(
                SentenceComplexity(
                    sentence=sentence,
                    position=i,
                    word_count=len(sentence.split()),
                    syllable_count=count_syllables(sentence),
                    complexity_score=complexity["score"],
                    issues=complexity["issues"],
                    suggestions=complexity["suggestions"]
                )
            )
        
        # Identify problem areas
        problem_sentences = [
            s for s in sentence_analyses
            if s.complexity_score > 0.7
        ]
        
        response = ReadabilityDetailedResponse(
            **basic_response.dict(),
            sentence_analyses=sentence_analyses,
            problem_sentences=problem_sentences,
            target_grade_level=target_grade_level,
            meets_target=_check_target_met(
                basic_response.grade_level,
                target_grade_level
            ) if target_grade_level else None
        )
        
        return response
        
    except Exception as e:
        logger.error(f"Error in detailed readability check: {e}")
        raise


async def get_readability_suggestions(
    text: str,
    target_grade_level: Optional[int] = None
) -> Dict:
    """
    Get suggestions to improve text readability.
    
    Args:
        text: Text to improve
        target_grade_level: Desired reading grade level
        
    Returns:
        Dictionary of suggestions
    """
    try:
        # Analyze current readability
        analysis = await check_readability_detailed(text, target_grade_level)
        
        suggestions = {
            "overall": [],
            "sentence_level": [],
            "word_level": []
        }
        
        # Overall suggestions
        if analysis.grade_level > (target_grade_level or 12):
            suggestions["overall"].append({
                "type": "grade_level",
                "message": f"Text is at grade level {analysis.grade_level:.1f}. " +
                          f"Target: {target_grade_level or 12}",
                "priority": "high"
            })
        
        if analysis.avg_words_per_sentence > 20:
            suggestions["overall"].append({
                "type": "sentence_length",
                "message": "Average sentence length is too long. Consider breaking up sentences.",
                "priority": "medium"
            })
        
        # Sentence-level suggestions
        for sentence in analysis.problem_sentences:
            suggestions["sentence_level"].extend([
                {
                    "sentence": sentence.sentence,
                    "position": sentence.position,
                    "issue": issue,
                    "suggestion": suggestion
                }
                for issue, suggestion in zip(sentence.issues, sentence.suggestions)
            ])
        
        # Word-level suggestions
        complex_words = _identify_complex_words(text)
        for word, replacement in complex_words:
            suggestions["word_level"].append({
                "word": word,
                "suggestion": replacement,
                "reason": "Use simpler alternatives"
            })
        
        return suggestions
        
    except Exception as e:
        logger.error(f"Error generating readability suggestions: {e}")
        raise


async def analyze_sentence_complexity(text: str) -> Dict:
    """
    Analyze sentence complexity and structure.
    
    Args:
        text: Text to analyze
        
    Returns:
        Complexity analysis for each sentence
    """
    try:
        sentences = split_into_sentences(text)
        
        analyses = []
        
        for i, sentence in enumerate(sentences):
            if len(sentence.strip()) < 3:
                continue
            
            analysis = _analyze_sentence_structure(sentence)
            analysis["sentence"] = sentence
            analysis["position"] = i
            
            analyses.append(analysis)
        
        return {
            "sentences": analyses,
            "average_complexity": sum(s["score"] for s in analyses) / len(analyses) if analyses else 0,
            "most_complex": max(analyses, key=lambda x: x["score"]) if analyses else None,
            "simplest": min(analyses, key=lambda x: x["score"]) if analyses else None
        }
        
    except Exception as e:
        logger.error(f"Error analyzing sentence complexity: {e}")
        raise


def _calculate_average_grade_level(metrics: ReadabilityMetrics) -> float:
    """Calculate average grade level from multiple metrics"""
    levels = [
        metrics.flesch_kincaid_grade,
        metrics.gunning_fog,
        metrics.smog_index,
        metrics.coleman_liau_index,
        metrics.automated_readability_index
    ]
    
    # Filter out extreme values
    valid_levels = [l for l in levels if 0 <= l <= 20]
    
    if not valid_levels:
        return 12.0
    
    return sum(valid_levels) / len(valid_levels)


def _estimate_reading_time(text: str) -> float:
    """Estimate reading time in minutes (average 200 words per minute)"""
    words = len(text.split())
    return words / 200.0


def _determine_difficulty_level(flesch_score: float) -> str:
    """Determine difficulty level from Flesch Reading Ease score"""
    if flesch_score >= 90:
        return "very_easy"
    elif flesch_score >= 80:
        return "easy"
    elif flesch_score >= 70:
        return "fairly_easy"
    elif flesch_score >= 60:
        return "standard"
    elif flesch_score >= 50:
        return "fairly_difficult"
    elif flesch_score >= 30:
        return "difficult"
    else:
        return "very_difficult"


def _generate_readability_recommendations(
    metrics: ReadabilityMetrics,
    text: str
) -> List[str]:
    """Generate recommendations based on readability metrics"""
    recommendations = []
    
    if metrics.flesch_reading_ease < 50:
        recommendations.append("Consider simplifying sentence structure")
    
    if metrics.flesch_kincaid_grade > 12:
        recommendations.append("Text may be too complex for general audience")
    
    sentences = split_into_sentences(text)
    avg_sentence_length = len(text.split()) / len(sentences) if sentences else 0
    
    if avg_sentence_length > 25:
        recommendations.append("Break up long sentences for better readability")
    
    return recommendations


def _analyze_sentence_structure(sentence: str) -> Dict:
    """Analyze the structure and complexity of a sentence"""
    words = sentence.split()
    word_count = len(words)
    
    # Count clauses (rough estimate based on conjunctions and punctuation)
    clause_indicators = [',', 'and', 'but', 'or', 'because', 'since', 'although', 'while']
    clause_count = 1 + sum(1 for indicator in clause_indicators if indicator in sentence.lower())
    
    # Calculate complexity score (0-1 scale)
    complexity_score = min(1.0, (word_count / 30.0 + clause_count / 5.0) / 2)
    
    issues = []
    suggestions = []
    
    if word_count > 30:
        issues.append("Sentence is too long")
        suggestions.append("Break into multiple shorter sentences")
    
    if clause_count > 3:
        issues.append("Too many clauses")
        suggestions.append("Simplify sentence structure")
    
    # Check for passive voice (simple check)
    if re.search(r'\b(is|are|was|were|been|being)\s+\w+ed\b', sentence):
        issues.append("Possible passive voice")
        suggestions.append("Consider using active voice")
    
    return {
        "score": complexity_score,
        "word_count": word_count,
        "clause_count": clause_count,
        "issues": issues,
        "suggestions": suggestions
    }


def _check_target_met(current_level: float, target_level: int) -> bool:
    """Check if current reading level meets target"""
    return current_level <= target_level + 1.0  # Allow 1 grade level tolerance


def _identify_complex_words(text: str) -> List[tuple]:
    """Identify complex words and suggest simpler alternatives"""
    complex_word_map = {
        "utilize": "use",
        "facilitate": "help",
        "demonstrate": "show",
        "implement": "do",
        "subsequently": "then",
        "approximately": "about",
        "commence": "start",
        "terminate": "end",
        "purchase": "buy",
        "endeavor": "try"
    }
    
    words = text.lower().split()
    suggestions = []
    
    for word in words:
        clean_word = re.sub(r'[^\w]', '', word)
        if clean_word in complex_word_map:
            suggestions.append((clean_word, complex_word_map[clean_word]))
    
    return suggestions
```

---

### **📁 services/ai_rewrite_service.py**

```python
"""
AI Rewrite Service
Handles AI-powered text rewriting and enhancement
"""
from typing import List, Dict, Optional
import openai
from transformers import pipeline

from models.response_models import (
    RewriteResponse,
    ParaphraseResponse,
    RewriteVariation
)
from models.enums import RewriteStyle, ToneType
from utils.cache import cache_result, get_cached_result
from utils.settings import settings
from utils.logger import setup_logger

logger = setup_logger(__name__)

# Initialize OpenAI if key is available
if settings.OPENAI_API_KEY:
    openai.api_key = settings.OPENAI_API_KEY


async def rewrite_text(
    text: str,
    style: RewriteStyle = RewriteStyle.IMPROVE,
    preserve_meaning: bool = True
) -> RewriteResponse:
    """
    Rewrite text using AI to improve clarity and quality.
    
    Args:
        text: Text to rewrite
        style: Rewriting style
        preserve_meaning: Whether to preserve original meaning strictly
        
    Returns:
        RewriteResponse with rewritten text
    """
    try:
        # Check cache
        cache_key = f"rewrite:{hash(text)}:{style}"
        cached_result = await get_cached_result(cache_key)
        if cached_result:
            logger.debug("Returning cached rewrite result")
            return RewriteResponse(**cached_result)
        
        # Generate prompt based on style
        prompt = _create_rewrite_prompt(text, style, preserve_meaning)
        
        # Use OpenAI if available, otherwise use placeholder
        if settings.OPENAI_API_KEY:
            rewritten = await _rewrite_with_openai(prompt)
        else:
            rewritten = _rewrite_placeholder(text, style)
        
        # Analyze changes
        changes = _analyze_changes(text, rewritten)
        
        response = RewriteResponse(
            original_text=text,
            rewritten_text=rewritten,
            style=style,
            changes_made=changes["count"],
            improvement_score=changes["improvement_score"],
            changes_summary=changes["summary"]
        )
        
        # Cache result
        await cache_result(cache_key, response.dict(), ttl=3600)
        
        logger.info(f"Text rewritten with style: {style}")
        
        return response
        
    except Exception as e:
        logger.error(f"Error in text rewriting: {e}")
        raise


async def paraphrase_text(
    text: str,
    num_variations: int = 3,
    creativity_level: float = 0.7
) -> ParaphraseResponse:
    """
    Generate paraphrased versions of the text.
    
    Args:
        text: Text to paraphrase
        num_variations: Number of variations to generate
        creativity_level: How creative the paraphrases should be (0.0-1.0)
        
    Returns:
        ParaphraseResponse with multiple paraphrases
    """
    try:
        variations = []
        
        for i in range(num_variations):
            if settings.OPENAI_API_KEY:
                prompt = f"Paraphrase the following text with creativity level {creativity_level}:\n\n{text}"
                paraphrased = await _rewrite_with_openai(prompt, temperature=creativity_level)
            else:
                paraphrased = f"Paraphrased version {i+1}: {text}"
            
            variation = RewriteVariation(
                text=paraphrased,
                variation_index=i,
                creativity_score=creativity_level,
                similarity_to_original=_calculate_similarity(text, paraphrased)
            )
            variations.append(variation)
        
        response = ParaphraseResponse(
            original_text=text,
            variations=variations,
            num_variations=len(variations)
        )
        
        logger.info(f"Generated {num_variations} paraphrases")
        
        return response
        
    except Exception as e:
        logger.error(f"Error in paraphrasing: {e}")
        raise


async def simplify_text(
    text: str,
    target_grade_level: int = 8

) -> Dict:
    """
    Simplify text to match a target reading grade level.
    
    Args:
        text: Text to simplify
        target_grade_level: Target reading grade level
        
    Returns:
        Dictionary with simplified text
    """
    try:
        prompt = f"""Simplify the following text to a {target_grade_level}th grade reading level.
Use simpler words and shorter sentences while preserving the original meaning.

Text: {text}"""
        
        if settings.OPENAI_API_KEY:
            simplified = await _rewrite_with_openai(prompt)
        else:
            simplified = f"Simplified version: {text}"
        
        return {
            "original_text": text,
            "simplified_text": simplified,
            "target_grade_level": target_grade_level,
            "changes_made": _analyze_changes(text, simplified)
        }
        
    except Exception as e:
        logger.error(f"Error in text simplification: {e}")
        raise


async def expand_text(
    text: str,
    expansion_factor: float = 1.5
) -> Dict:
    """
    Expand text by adding more detail and context.
    
    Args:
        text: Text to expand
        expansion_factor: How much to expand (1.0 = no change, 3.0 = 3x longer)
        
    Returns:
        Dictionary with expanded text
    """
    try:
        prompt = f"""Expand the following text by adding more detail, context, and explanation.
Make it approximately {expansion_factor}x longer while maintaining coherence.

Text: {text}"""
        
        if settings.OPENAI_API_KEY:
            expanded = await _rewrite_with_openai(prompt)
        else:
            expanded = f"Expanded version: {text} " * int(expansion_factor)
        
        return {
            "original_text": text,
            "expanded_text": expanded,
            "expansion_factor": expansion_factor,
            "original_word_count": len(text.split()),
            "expanded_word_count": len(expanded.split())
        }
        
    except Exception as e:
        logger.error(f"Error in text expansion: {e}")
        raise


async def rewrite_with_style(
    text: str,
    target_tone: ToneType,
    target_style: RewriteStyle
) -> Dict:
    """
    Rewrite text to match specific tone and style.
    
    Args:
        text: Text to rewrite
        target_tone: Desired tone
        target_style: Desired style
        
    Returns:
        Dictionary with rewritten text
    """
    try:
        prompt = f"""Rewrite the following text with a {target_tone.value} tone and {target_style.value} style.

Text: {text}"""
        
        if settings.OPENAI_API_KEY:
            rewritten = await _rewrite_with_openai(prompt)
        else:
            rewritten = f"Rewritten ({target_tone.value}, {target_style.value}): {text}"
        
        return {
            "original_text": text,
            "rewritten_text": rewritten,
            "target_tone": target_tone.value,
            "target_style": target_style.value,
            "changes_made": _analyze_changes(text, rewritten)
        }
        
    except Exception as e:
        logger.error(f"Error in style rewriting: {e}")
        raise


def _create_rewrite_prompt(
    text: str,
    style: RewriteStyle,
    preserve_meaning: bool
) -> str:
    """Create prompt for AI rewriting based on style"""
    style_prompts = {
        RewriteStyle.IMPROVE: "Improve the clarity and quality of the following text:",
        RewriteStyle.SIMPLIFY: "Simplify the following text using easier words and shorter sentences:",
        RewriteStyle.EXPAND: "Expand the following text with more detail and context:",
        RewriteStyle.FORMAL: "Rewrite the following text in a formal, professional style:",
        RewriteStyle.CASUAL: "Rewrite the following text in a casual, conversational style:"
    }
    
    prompt = style_prompts.get(style, "Improve the following text:")
    prompt += f"\n\n{text}"
    
    if preserve_meaning:
        prompt += "\n\nIMPORTANT: Preserve the original meaning and key information."
    
    return prompt


async def _rewrite_with_openai(prompt: str, temperature: float = 0.7) -> str:
    """Use OpenAI API to rewrite text"""
    try:
        response = await openai.ChatCompletion.acreate(
            model="gpt-3.5-turbo",
            messages=[
                {"role": "system", "content": "You are a professional writing assistant."},
                {"role": "user", "content": prompt}
            ],
            temperature=temperature,
            max_tokens=1000
        )
        
        return response.choices[0].message.content.strip()
        
    except Exception as e:
        logger.error(f"Error using OpenAI API: {e}")
        raise


def _rewrite_placeholder(text: str, style: RewriteStyle) -> str:
    """Placeholder rewriting when OpenAI is not available"""
    style_prefixes = {
        RewriteStyle.IMPROVE: "Improved: ",
        RewriteStyle.SIMPLIFY: "Simplified: ",
        RewriteStyle.EXPAND: "Expanded: ",
        RewriteStyle.FORMAL: "Formal: ",
        RewriteStyle.CASUAL: "Casual: "
    }
    
    prefix = style_prefixes.get(style, "Rewritten: ")
    return prefix + text.capitalize()


def _analyze_changes(original: str, rewritten: str) -> Dict:
    """Analyze changes between original and rewritten text"""
    orig_words = original.split()
    rewr_words = rewritten.split()
    
    # Calculate differences
    changes_count = abs(len(orig_words) - len(rewr_words))
    
    # Calculate similarity
    similarity = _calculate_similarity(original, rewritten)
    
    # Improvement score (placeholder - would use more sophisticated metrics)
    improvement_score = 0.7 if len(rewr_words) > len(orig_words) else 0.8
    
    return {
        "count": changes_count,
        "improvement_score": improvement_score,
        "summary": {
            "original_word_count": len(orig_words),
            "rewritten_word_count": len(rewr_words),
            "similarity": similarity
        }
    }


def _calculate_similarity(text1: str, text2: str) -> float:
    """Calculate similarity between two texts (simple Jaccard similarity)"""
    words1 = set(text1.lower().split())
    words2 = set(text2.lower().split())
    
    if not words1 or not words2:
        return 0.0
    
    intersection = words1.intersection(words2)
    union = words1.union(words2)
    
    return len(intersection) / len(union) if union else 0.0
```

---

I'll continue with more service files in the next segment. Would you like me to proceed with the remaining services, models, utils, and core files?

## **SEGMENT 6: Service Files (Continued)**

### **📁 services/text_analysis_service.py**

```python
"""
Text Analysis Service
Handles comprehensive text analysis and analytics
"""
from typing import List, Dict, Optional
from datetime import datetime, timedelta
from sqlalchemy import func

from models.response_models import (
    AnalyticsResponse,
    UsageStatsResponse,
    TrendAnalysisResponse
)
from database.repositories.user_repository import UserRepository
from database.repositories.suggestion_repository import SuggestionRepository
from utils.logger import setup_logger
from core.database import get_db

logger = setup_logger(__name__)


async def get_user_analytics(
    user_id: str,
    period: str = "30d"
) -> AnalyticsResponse:
    """
    Get comprehensive analytics for a user.
    
    Args:
        user_id: User ID
        period: Time period (7d, 30d, 90d, 1y)
        
    Returns:
        AnalyticsResponse with user analytics
    """
    try:
        # Parse period
        days = _parse_period(period)
        start_date = datetime.utcnow() - timedelta(days=days)
        
        # Fetch user data from repositories
        async for db in get_db():
            suggestion_repo = SuggestionRepository(db)
            
            # Get suggestion statistics
            total_suggestions = await suggestion_repo.count_user_suggestions(
                user_id,
                start_date
            )
            
            accepted_suggestions = await suggestion_repo.count_accepted_suggestions(
                user_id,
                start_date
            )
            
            # Get error breakdown by type
            error_breakdown = await suggestion_repo.get_error_breakdown(
                user_id,
                start_date
            )
            
            # Calculate improvement metrics
            improvement_rate = (
                (accepted_suggestions / total_suggestions * 100)
                if total_suggestions > 0 else 0
            )
            
            return AnalyticsResponse(
                user_id=user_id,
                period=period,
                total_checks=total_suggestions,
                total_errors_found=total_suggestions,
                errors_corrected=accepted_suggestions,
                improvement_rate=improvement_rate,
                error_breakdown=error_breakdown,
                most_common_errors=await _get_most_common_errors(
                    user_id,
                    start_date,
                    suggestion_repo
                ),
                writing_score=_calculate_writing_score(
                    total_suggestions,
                    accepted_suggestions
                )
            )
        
    except Exception as e:
        logger.error(f"Error getting user analytics: {e}")
        raise


async def get_usage_statistics(
    user_id: str,
    start_date: datetime,
    end_date: datetime
) -> UsageStatsResponse:
    """
    Get usage statistics for a user within a date range.
    
    Args:
        user_id: User ID
        start_date: Start date
        end_date: End date
        
    Returns:
        UsageStatsResponse with usage statistics
    """
    try:
        async for db in get_db():
            suggestion_repo = SuggestionRepository(db)
            
            # Get daily usage
            daily_usage = await suggestion_repo.get_daily_usage(
                user_id,
                start_date,
                end_date
            )
            
            # Calculate totals
            total_api_calls = sum(day["count"] for day in daily_usage)
            
            # Get feature usage breakdown
            feature_usage = await suggestion_repo.get_feature_usage(
                user_id,
                start_date,
                end_date
            )
            
            # Calculate average response time
            avg_response_time = await suggestion_repo.get_avg_response_time(
                user_id,
                start_date,
                end_date
            )
            
            return UsageStatsResponse(
                user_id=user_id,
                start_date=start_date,
                end_date=end_date,
                total_api_calls=total_api_calls,
                daily_usage=daily_usage,
                feature_usage=feature_usage,
                avg_response_time_ms=avg_response_time,
                peak_usage_day=max(daily_usage, key=lambda x: x["count"])["date"] if daily_usage else None
            )
        
    except Exception as e:
        logger.error(f"Error getting usage statistics: {e}")
        raise


async def get_error_trends(
    user_id: str,
    error_type: Optional[str] = None
) -> TrendAnalysisResponse:
    """
    Analyze trends in writing errors over time.
    
    Args:
        user_id: User ID
        error_type: Optional filter for specific error type
        
    Returns:
        TrendAnalysisResponse with trend data
    """
    try:
        async for db in get_db():
            suggestion_repo = SuggestionRepository(db)
            
            # Get error trends over last 90 days
            trends = await suggestion_repo.get_error_trends(
                user_id,
                days=90,
                error_type=error_type
            )
            
            # Calculate trend direction
            trend_direction = _calculate_trend_direction(trends)
            
            return TrendAnalysisResponse(
                user_id=user_id,
                error_type=error_type,
                trends=trends,
                trend_direction=trend_direction,
                improvement_percentage=_calculate_improvement_percentage(trends)
            )
        
    except Exception as e:
        logger.error(f"Error analyzing error trends: {e}")
        raise


async def get_improvement_metrics(user_id: str) -> Dict:
    """
    Get metrics showing writing improvement over time.
    
    Args:
        user_id: User ID
        
    Returns:
        Dictionary with improvement metrics
    """
    try:
        async for db in get_db():
            suggestion_repo = SuggestionRepository(db)
            
            # Compare first 30 days vs last 30 days
            now = datetime.utcnow()
            
            # Get recent stats (last 30 days)
            recent_start = now - timedelta(days=30)
            recent_stats = await suggestion_repo.get_period_stats(
                user_id,
                recent_start,
                now
            )
            
            # Get historical stats (first 30 days of usage)
            first_check = await suggestion_repo.get_first_check_date(user_id)
            if first_check:
                historical_end = first_check + timedelta(days=30)
                historical_stats = await suggestion_repo.get_period_stats(
                    user_id,
                    first_check,
                    historical_end
                )
            else:
                historical_stats = {"avg_errors": 0, "acceptance_rate": 0}
            
            # Calculate improvement
            error_reduction = (
                ((historical_stats.get("avg_errors", 0) - recent_stats.get("avg_errors", 0))
                / historical_stats.get("avg_errors", 1)) * 100
                if historical_stats.get("avg_errors", 0) > 0 else 0
            )
            
            return {
                "user_id": user_id,
                "historical_period": {
                    "avg_errors_per_check": historical_stats.get("avg_errors", 0),
                    "acceptance_rate": historical_stats.get("acceptance_rate", 0)
                },
                "recent_period": {
                    "avg_errors_per_check": recent_stats.get("avg_errors", 0),
                    "acceptance_rate": recent_stats.get("acceptance_rate", 0)
                },
                "improvement": {
                    "error_reduction_percentage": error_reduction,
                    "acceptance_rate_change": (
                        recent_stats.get("acceptance_rate", 0) -
                        historical_stats.get("acceptance_rate", 0)
                    ),
                    "overall_score": _calculate_overall_improvement_score(
                        error_reduction,
                        recent_stats,
                        historical_stats
                    )
                }
            }
        
    except Exception as e:
        logger.error(f"Error getting improvement metrics: {e}")
        raise


async def analyze_document_full(
    text: str,
    full_analysis: bool = True
) -> Dict:
    """
    Perform comprehensive analysis on a document.
    
    Args:
        text: Document text
        full_analysis: Whether to perform full analysis
        
    Returns:
        Dictionary with all analysis results
    """
    try:
        from services.spelling_service import check_spelling
        from services.grammar_service import check_grammar
        from services.tone_service import analyze_tone
        from services.readability_service import check_readability
        
        # Run all checks concurrently
        import asyncio
        
        spelling_task = check_spelling(text)
        grammar_task = check_grammar(text)
        tone_task = analyze_tone(text)
        readability_task = check_readability(text)
        
        results = await asyncio.gather(
            spelling_task,
            grammar_task,
            tone_task,
            readability_task,
            return_exceptions=True
        )
        
        # Extract results
        spelling_result = results[0] if not isinstance(results[0], Exception) else None
        grammar_result = results[1] if not isinstance(results[1], Exception) else None
        tone_result = results[2] if not isinstance(results[2], Exception) else None
        readability_result = results[3] if not isinstance(results[3], Exception) else None
        
        # Calculate overall score
        overall_score = _calculate_document_score(
            spelling_result,
            grammar_result,
            readability_result
        )
        
        return {
            "spelling": spelling_result.dict() if spelling_result else None,
            "grammar": grammar_result.dict() if grammar_result else None,
            "tone": tone_result.dict() if tone_result else None,
            "readability": readability_result.dict() if readability_result else None,
            "overall_score": overall_score,
            "summary": {
                "total_issues": (
                    (spelling_result.total_issues if spelling_result else 0) +
                    (grammar_result.total_issues if grammar_result else 0)
                ),
                "critical_issues": grammar_result.critical_issues if grammar_result else 0,
                "primary_tone": tone_result.primary_tone if tone_result else None,
                "grade_level": readability_result.grade_level if readability_result else None
            }
        }
        
    except Exception as e:
        logger.error(f"Error in full document analysis: {e}")
        raise


def _parse_period(period: str) -> int:
    """Parse period string to number of days"""
    period_map = {
        "7d": 7,
        "30d": 30,
        "90d": 90,
        "1y": 365
    }
    return period_map.get(period, 30)


async def _get_most_common_errors(
    user_id: str,
    start_date: datetime,
    repo
) -> List[Dict]:
    """Get most common errors for a user"""
    return await repo.get_most_common_errors(user_id, start_date, limit=10)


def _calculate_writing_score(total_checks: int, corrected: int) -> float:
    """Calculate overall writing score (0-100)"""
    if total_checks == 0:
        return 100.0
    
    error_rate = corrected / total_checks
    
    # Score formula: lower error rate = higher score
    score = max(0, min(100, 100 - (error_rate * 50)))
    
    return round(score, 1)


def _calculate_trend_direction(trends: List[Dict]) -> str:
    """Calculate trend direction (improving, declining, stable)"""
    if len(trends) < 2:
        return "insufficient_data"
    
    # Compare first half vs second half
    mid_point = len(trends) // 2
    first_half_avg = sum(t["count"] for t in trends[:mid_point]) / mid_point
    second_half_avg = sum(t["count"] for t in trends[mid_point:]) / (len(trends) - mid_point)
    
    change_percentage = ((first_half_avg - second_half_avg) / first_half_avg * 100) if first_half_avg > 0 else 0
    
    if change_percentage > 10:
        return "improving"
    elif change_percentage < -10:
        return "declining"
    else:
        return "stable"


def _calculate_improvement_percentage(trends: List[Dict]) -> float:
    """Calculate improvement percentage over time"""
    if len(trends) < 2:
        return 0.0
    
    first = trends[0]["count"]
    last = trends[-1]["count"]
    
    if first == 0:
        return 0.0
    
    improvement = ((first - last) / first) * 100
    
    return round(improvement, 1)


def _calculate_overall_improvement_score(
    error_reduction: float,
    recent_stats: Dict,
    historical_stats: Dict
) -> float:
    """Calculate overall improvement score (0-100)"""
    # Weight error reduction at 60%, acceptance rate improvement at 40%
    acceptance_improvement = (
        (recent_stats.get("acceptance_rate", 0) - historical_stats.get("acceptance_rate", 0))
        * 100
    )
    
    score = (error_reduction * 0.6) + (acceptance_improvement * 0.4)
    
    return max(0, min(100, score))


def _calculate_document_score(
    spelling_result,
    grammar_result,
    readability_result
) -> Dict:
    """Calculate overall document quality score"""
    scores = {
        "spelling": 100,
        "grammar": 100,
        "readability": 50,
        "overall": 0
    }
    
    # Spelling score
    if spelling_result:
        word_count = len(spelling_result.text.split())
        error_rate = spelling_result.total_issues / word_count if word_count > 0 else 0
        scores["spelling"] = max(0, 100 - (error_rate * 100))
    
    # Grammar score
    if grammar_result:
        word_count = len(grammar_result.text.split())
        error_rate = grammar_result.total_issues / word_count if word_count > 0 else 0
        scores["grammar"] = max(0, 100 - (error_rate * 100))
    
    # Readability score (based on grade level)
    if readability_result:
        # Ideal grade level is 8-10
        grade_level = readability_result.grade_level
        if 8 <= grade_level <= 10:
            scores["readability"] = 100
        elif grade_level < 8:
            scores["readability"] = 80 + (grade_level - 6) * 10
        else:
            scores["readability"] = max(0, 100 - (grade_level - 10) * 5)
    
    # Calculate overall
    scores["overall"] = (
        scores["spelling"] * 0.4 +
        scores["grammar"] * 0.4 +
        scores["readability"] * 0.2
    )
    
    return scores
```

---

### **📁 services/suggestion_service.py**

```python
"""
Suggestion Service
Handles user management and suggestion tracking
"""
from typing import Optional, Dict
from datetime import datetime, timedelta
import hashlib
import secrets

from models.request_models import (
    UserCreateRequest,
    UserUpdateRequest
)
from models.response_models import (
    UserResponse,
    UserProfileResponse,
    TokenResponse
)
from models.database_models import User
from database.repositories.user_repository import UserRepository
from core.database import get_db
from core.security import hash_password, verify_password, create_access_token
from utils.logger import setup_logger

logger = setup_logger(__name__)


async def create_user(data: UserCreateRequest) -> UserResponse:
    """
    Create a new user.
    
    Args:
        data: User creation data
        
    Returns:
        UserResponse with created user info
    """
    try:
        async for db in get_db():
            user_repo = UserRepository(db)
            
            # Check if user already exists
            existing_user = await user_repo.get_by_email(data.email)
            if existing_user:
                raise ValueError("User with this email already exists")
            
            # Hash password
            hashed_password = hash_password(data.password)
            
            # Create user
            user = await user_repo.create(
                email=data.email,
                hashed_password=hashed_password,
                full_name=data.full_name
            )
            
            return UserResponse(
                id=user.id,
                email=user.email,
                full_name=user.full_name,
                is_active=user.is_active,
                created_at=user.created_at
            )
        
    except Exception as e:
        logger.error(f"Error creating user: {e}")
        raise


async def authenticate_user(email: str, password: str) -> Optional[TokenResponse]:
    """
    Authenticate user and return tokens.
    
    Args:
        email: User email
        password: User password
        
    Returns:
        TokenResponse with access and refresh tokens
    """
    try:
        async for db in get_db():
            user_repo = UserRepository(db)
            
            # Get user by email
            user = await user_repo.get_by_email(email)
            if not user:
                return None
            
            # Verify password
            if not verify_password(password, user.hashed_password):
                return None
            
            # Update last login
            await user_repo.update_last_login(user.id)
            
            # Create tokens
            access_token = create_access_token({"sub": user.id})
            refresh_token = secrets.token_urlsafe(32)
            
            # Store refresh token (in production, store in database)
            
            return TokenResponse(
                access_token=access_token,
                refresh_token=refresh_token,
                token_type="bearer",
                expires_in=1800  # 30 minutes
            )
        
    except Exception as e:
        logger.error(f"Error authenticating user: {e}")
        raise


async def get_user_profile(user_id: str) -> UserProfileResponse:
    """
    Get user profile with statistics.
    
    Args:
        user_id: User ID
        
    Returns:
        UserProfileResponse with profile data
    """
    try:
        async for db in get_db():
            user_repo = UserRepository(db)
            
            user = await user_repo.get_by_id(user_id)
            if not user:
                raise ValueError("User not found")
            
            # Get user statistics
            stats = await user_repo.get_user_statistics(user_id)
            
            return UserProfileResponse(
                id=user.id,
                email=user.email,
                full_name=user.full_name,
                is_active=user.is_active,
                created_at=user.created_at,
                last_login=user.last_login,
                preferences=user.preferences or {},
                statistics=stats
            )
        
    except Exception as e:
        logger.error(f"Error getting user profile: {e}")
        raise


async def update_user(
    user_id: str,
    data: UserUpdateRequest
) -> UserResponse:
    """
    Update user information.
    
    Args:
        user_id: User ID
        data: Update data
        
    Returns:
        UserResponse with updated user info
    """
    try:
        async for db in get_db():
            user_repo = UserRepository(db)
            
            # Get existing user
            user = await user_repo.get_by_id(user_id)
            if not user:
                raise ValueError("User not found")
            
            # Update fields
            update_dict = {}
            if data.full_name:
                update_dict["full_name"] = data.full_name
            if data.email:
                # Check if new email is already taken
                existing = await user_repo.get_by_email(data.email)
                if existing and existing.id != user_id:
                    raise ValueError("Email already in use")
                update_dict["email"] = data.email
            if data.preferences:
                update_dict["preferences"] = data.preferences
            
            # Update user
            updated_user = await user_repo.update(user_id, update_dict)
            
            return UserResponse(
                id=updated_user.id,
                email=updated_user.email,
                full_name=updated_user.full_name,
                is_active=updated_user.is_active,
                created_at=updated_user.created_at
            )
        
    except Exception as e:
        logger.error(f"Error updating user: {e}")
        raise


async def delete_user(user_id: str) -> bool:
    """
    Delete user account.
    
    Args:
        user_id: User ID
        
    Returns:
        True if successful
    """
    try:
        async for db in get_db():
            user_repo = UserRepository(db)
            
            success = await user_repo.delete(user_id)
            
            if success:
                logger.info(f"User deleted: {user_id}")
            
            return success
        
    except Exception as e:
        logger.error(f"Error deleting user: {e}")
        raise


async def refresh_access_token(refresh_token: str) -> Optional[TokenResponse]:
    """
    Refresh access token using refresh token.
    
    Args:
        refresh_token: Valid refresh token
        
    Returns:
        New TokenResponse or None if invalid
    """
    try:
        # In production, validate refresh token from database
        # For now, return None (not implemented)
        logger.warning("Refresh token functionality not fully implemented")
        return None
        
    except Exception as e:
        logger.error(f"Error refreshing token: {e}")
        raise
```

---

### **📁 services/context_service.py**

```python
"""
Context Service
Handles document management and context analysis
"""
from typing import List, Dict, Optional
from datetime import datetime
import uuid

from models.request_models import DocumentCreateRequest, DocumentUpdateRequest
from models.response_models import DocumentResponse, DocumentListResponse
from models.database_models import Document
from database.repositories.document_repository import DocumentRepository
from core.database import get_db
from utils.logger import setup_logger
from utils.text_processing import extract_text_from_file

logger = setup_logger(__name__)


async def create_document(
    user_id: str,
    data: DocumentCreateRequest
) -> DocumentResponse:
    """
    Create a new document.
    
    Args:
        user_id: Owner user ID
        data: Document creation data
        
    Returns:
        DocumentResponse with created document
    """
    try:
        async for db in get_db():
            doc_repo = DocumentRepository(db)
            
            document = await doc_repo.create(
                user_id=user_id,
                title=data.title,
                content=data.content,
                tags=data.tags or []
            )
            
            logger.info(f"Document created: {document.id}")
            
            return _document_to_response(document)
        
    except Exception as e:
        logger.error(f"Error creating document: {e}")
        raise


async def get_document(
    document_id: str,
    user_id: str
) -> Optional[DocumentResponse]:
    """
    Get a specific document by ID.
    
    Args:
        document_id: Document ID
        user_id: User ID (for authorization)
        
    Returns:
        DocumentResponse or None if not found
    """
    try:
        async for db in get_db():
            doc_repo = DocumentRepository(db)
            
            document = await doc_repo.get_by_id(document_id, user_id)
            
            if not document:
                return None
            
            return _document_to_response(document)
        
    except Exception as e:
        logger.error(f"Error getting document: {e}")
        raise


async def update_document(
    document_id: str,
    user_id: str,
    data: DocumentUpdateRequest
) -> Optional[DocumentResponse]:
    """
    Update a document.
    
    Args:
        document_id: Document ID
        user_id: User ID (for authorization)
        data: Update data
        
    Returns:
        DocumentResponse or None if not found
    """
    try:
        async for db in get_db():
            doc_repo = DocumentRepository(db)
            
            # Check document exists and belongs to user
            document = await doc_repo.get_by_id(document_id, user_id)
            if not document:
                return None
            
            # Update fields
            update_dict = {}
            if data.title:
                update_dict["title"] = data.title
            if data.content:
                update_dict["content"] = data.content
            if data.tags is not None:
                update_dict["tags"] = data.tags
            
            updated_document = await doc_repo.update(document_id, update_dict)
            
            logger.info(f"Document updated: {document_id}")
            
            return _document_to_response(updated_document)
        
    except Exception as e:
        logger.error(f"Error updating document: {e}")
        raise


async def delete_document(
    document_id: str,
    user_id: str
) -> bool:
    """
    Delete a document.
    
    Args:
        document_id: Document ID
        user_id: User ID (for authorization)
        
    Returns:
        True if successful
    """
    try:
        async for db in get_db():
            doc_repo = DocumentRepository(db)
            
            success = await doc_repo.delete(document_id, user_id)
            
            if success:
                logger.info(f"Document deleted: {document_id}")
            
            return success
        
    except Exception as e:
        logger.error(f"Error deleting document: {e}")
        raise


async def list_user_documents(
    user_id: str,
    skip: int = 0,
    limit: int = 100,
    search: Optional[str] = None,
    tags: Optional[List[str]] = None
) -> DocumentListResponse:
    """
    List documents for a user with filtering.
    
    Args:
        user_id: User ID
        skip: Number to skip (pagination)
        limit: Maximum number to return
        search: Search query
        tags: Filter by tags
        
    Returns:
        DocumentListResponse with documents
    """
    try:
        async for db in get_db():
            doc_repo = DocumentRepository(db)
            
            documents = await doc_repo.list_by_user(
                user_id,
                skip=skip,
                limit=limit,
                search=search,
                tags=tags
            )
            
            total = await doc_repo.count_user_documents(user_id, search, tags)
            
            return DocumentListResponse(
                documents=[_document_to_response(doc) for doc in documents],
                total=total,
                skip=skip,
                limit=limit
            )
        
    except Exception as e:
        logger.error(f"Error listing documents: {e}")
        raise


async def process_uploaded_file(
    user_id: str,
    filename: str,
    content: bytes
) -> DocumentResponse:
    """
    Process an uploaded file and create a document.
    
    Args:
        user_id: User ID
        filename: Original filename
        content: File content bytes
        
    Returns:
        DocumentResponse with created document
    """
    try:
        # Extract text from file
        text_content = extract_text_from_file(filename, content)
        
        # Create document
        async for db in get_db():
            doc_repo = DocumentRepository(db)
            
            document = await doc_repo.create(
                user_id=user_id,
                title=filename,
                content=text_content,
                tags=["uploaded"]
            )
            
            logger.info(f"File processed and document created: {document.id}")
            
            return _document_to_response(document)
        
    except Exception as e:
        logger.error(f"Error processing uploaded file: {e}")
        raise


def _document_to_response(document: Document) -> DocumentResponse:
    """Convert Document model to DocumentResponse"""
    return DocumentResponse(
        id=document.id,
        user_id=document.user_id,
        title=document.title,
        content=document.content,
        tags=document.tags or [],
        created_at=document.created_at,
        updated_at=document.updated_at,
        word_count=len(document.content.split()),
        character_count=len(document.content)
    )
```

---

### **📁 services/plagiarism_service.py**

```python
"""
Plagiarism Service
Handles plagiarism detection and similarity checking
"""
from typing import List, Dict, Optional
import hashlib

from utils.logger import setup_logger

logger = setup_logger(__name__)


async def check_plagiarism(
    text: str,
    sources: Optional[List[str]] = None
) -> Dict:
    """
    Check text for potential plagiarism.
    
    Args:
        text: Text to check
        sources: Optional list of sources to check against
        
    Returns:
        Dictionary with plagiarism check results
    """
    try:
        # Placeholder implementation
        # In production, this would use services like Copyscape or Turnitin API
        
        similarity_score = 0.0  # 0-100 scale
        matches = []
        
        logger.info("Plagiarism check completed")
        
        return {
            "text": text,
            "similarity_score": similarity_score,
            "is_plagiarized": similarity_score > 30,
            "matches": matches,
            "sources_checked": len(sources) if sources else 0
        }
        
    except Exception as e:
        logger.error(f"Error checking plagiarism: {e}")
        raise


async def calculate_similarity(
    text1: str,
    text2: str
) -> float:
    """
    Calculate similarity between two texts.
    
    Args:
        text1: First text
        text2: Second text
        
    Returns:
        Similarity score (0.0-1.0)
    """
    try:
        # Simple Jaccard similarity
        words1 = set(text1.lower().split())
        words2 = set(text2.lower().split())
        
        intersection = words1.intersection(words2)
        union = words1.union(words2)
        
        similarity = len(intersection) / len(union) if union else 0.0
        
        return similarity
        
    except Exception as e:
        logger.error(f"Error calculating similarity: {e}")
        raise
```

---

### **📁 services/style_service.py**

```python
"""
Style Service
Handles writing style analysis and suggestions
"""
from typing import Dict, List
import re

from utils.logger import setup_logger

logger = setup_logger(__name__)


async def analyze_style(text: str) -> Dict:
    """
    Analyze writing style of the text.
    
    Args:
        text: Text to analyze
        
    Returns:
        Dictionary with style analysis
    """
    try:
        # Analyze various style metrics
        metrics = {
            "passive_voice_percentage": _calculate_passive_voice_percentage(text),
            "adverb_usage": _count_adverbs(text),
            "sentence_variety": _analyze_sentence_variety(text),
            "word_repetition": _analyze_word_repetition(text


),
            "transition_words": _count_transition_words(text),
            "cliches": _detect_cliches(text)
        }
        
        # Generate style recommendations
        recommendations = _generate_style_recommendations(metrics)
        
        logger.info("Style analysis completed")
        
        return {
            "metrics": metrics,
            "recommendations": recommendations,
            "overall_style_score": _calculate_style_score(metrics)
        }
        
    except Exception as e:
        logger.error(f"Error analyzing style: {e}")
        raise


def _calculate_passive_voice_percentage(text: str) -> float:
    """Calculate percentage of passive voice usage"""
    # Simple pattern matching for passive voice
    passive_pattern = r'\b(is|are|was|were|been|being)\s+\w+ed\b'
    passive_count = len(re.findall(passive_pattern, text, re.IGNORECASE))
    
    sentences = text.split('.')
    total_sentences = len([s for s in sentences if s.strip()])
    
    if total_sentences == 0:
        return 0.0
    
    return (passive_count / total_sentences) * 100


def _count_adverbs(text: str) -> int:
    """Count adverbs in text"""
    # Simple pattern for words ending in -ly
    adverb_pattern = r'\b\w+ly\b'
    return len(re.findall(adverb_pattern, text, re.IGNORECASE))


def _analyze_sentence_variety(text: str) -> Dict:
    """Analyze variety in sentence structure"""
    sentences = [s.strip() for s in text.split('.') if s.strip()]
    
    if not sentences:
        return {"variety_score": 0, "avg_length": 0, "length_variation": 0}
    
    lengths = [len(s.split()) for s in sentences]
    avg_length = sum(lengths) / len(lengths)
    length_variation = max(lengths) - min(lengths) if lengths else 0
    
    variety_score = min(100, length_variation * 5)  # Higher variation = more variety
    
    return {
        "variety_score": variety_score,
        "avg_length": avg_length,
        "length_variation": length_variation
    }


def _analyze_word_repetition(text: str) -> Dict:
    """Analyze word repetition in text"""
    words = text.lower().split()
    word_counts = {}
    
    for word in words:
        clean_word = re.sub(r'[^\w]', '', word)
        if len(clean_word) > 3:  # Only count words longer than 3 characters
            word_counts[clean_word] = word_counts.get(clean_word, 0) + 1
    
    # Find most repeated words
    repeated_words = {
        word: count for word, count in word_counts.items()
        if count > 2
    }
    
    return {
        "repeated_words": repeated_words,
        "repetition_score": min(100, len(repeated_words) * 10)
    }


def _count_transition_words(text: str) -> int:
    """Count transition words in text"""
    transition_words = [
        'however', 'therefore', 'furthermore', 'moreover',
        'consequently', 'nevertheless', 'additionally', 'finally'
    ]
    
    text_lower = text.lower()
    return sum(1 for word in transition_words if word in text_lower)


def _detect_cliches(text: str) -> List[str]:
    """Detect common cliches in text"""
    common_cliches = [
        'at the end of the day',
        'think outside the box',
        'low-hanging fruit',
        'paradigm shift',
        'circle back',
        'touch base',
        'synergy',
        'leverage'
    ]
    
    text_lower = text.lower()
    found_cliches = [cliche for cliche in common_cliches if cliche in text_lower]
    
    return found_cliches


def _generate_style_recommendations(metrics: Dict) -> List[str]:
    """Generate style improvement recommendations"""
    recommendations = []
    
    if metrics["passive_voice_percentage"] > 20:
        recommendations.append("Reduce passive voice usage for more direct writing")
    
    if metrics["adverb_usage"] > len(metrics["word_repetition"]["repeated_words"]) * 0.1:
        recommendations.append("Consider reducing adverb usage for stronger verbs")
    
    if metrics["sentence_variety"]["variety_score"] < 30:
        recommendations.append("Vary sentence length and structure for better flow")
    
    if metrics["transition_words"] < 2:
        recommendations.append("Add transition words to improve text flow")
    
    if len(metrics["cliches"]) > 0:
        recommendations.append(f"Avoid cliches: {', '.join(metrics['cliches'])}")
    
    return recommendations


def _calculate_style_score(metrics: Dict) -> float:
    """Calculate overall style quality score (0-100)"""
    score = 100.0
    
    # Deduct points for issues
    score -= min(20, metrics["passive_voice_percentage"])
    score -= min(10, metrics["adverb_usage"] * 0.5)
    score -= max(0, 30 - metrics["sentence_variety"]["variety_score"])
    score -= len(metrics["cliches"]) * 5
    
    return max(0, min(100, score))
```

---

Let me continue with the remaining files in the next segment. Would you like me to proceed with Models, Utils, Core, and ML Models?




        
