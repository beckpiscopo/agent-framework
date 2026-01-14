# FastAPI Best Practices Reference

## Table of Contents
1. [Project Structure](#1-project-structure)
2. [Routing & Endpoints](#2-routing--endpoints)
3. [Pydantic Models & Validation](#3-pydantic-models--validation)
4. [Dependency Injection](#4-dependency-injection)
5. [Error Handling](#5-error-handling)
6. [Database Integration](#6-database-integration)
7. [Performance & Async](#7-performance--async)
8. [Testing](#8-testing)
9. [Security](#9-security)
10. [Configuration](#10-configuration)
11. [Anti-Patterns](#11-anti-patterns)

---

## 1. Project Structure

### Small Projects (File-Type Structure)
```
app/
├── main.py           # FastAPI app instance
├── routers/          # API route handlers
├── models.py         # SQLAlchemy models
├── schemas.py        # Pydantic schemas
├── database.py       # Database configuration
├── dependencies.py   # Shared dependencies
└── config.py         # Settings
```

### Larger Projects (Domain-Based Structure)
```
src/
├── habits/
│   ├── router.py
│   ├── schemas.py
│   ├── models.py
│   ├── service.py
│   └── dependencies.py
├── users/
│   └── (same structure)
├── shared/
│   ├── config.py
│   ├── database.py
│   └── exceptions.py
└── main.py
```

### Key Principles
- Separation of concerns: routing, models, schemas, services separate
- One router per domain
- Explicit imports: use full module paths

```python
# GOOD
from src.habits import service as habits_service

# AVOID
from src.habits.service import *
```

---

## 2. Routing & Endpoints

### Using APIRouter

```python
from fastapi import APIRouter, status

router = APIRouter(
    prefix="/habits",
    tags=["habits"],
)

@router.get("/", response_model=list[HabitResponse])
async def list_habits():
    pass

@router.post("/", response_model=HabitResponse, status_code=status.HTTP_201_CREATED)
async def create_habit(habit: HabitCreate):
    pass
```

### Include in Main App

```python
from fastapi import FastAPI
from .routers import habits, users

app = FastAPI()
app.include_router(habits.router, prefix="/api")
app.include_router(users.router, prefix="/api")
```

### Path vs Query Parameters

```python
from typing import Annotated
from fastapi import Path, Query

@router.get("/habits/{habit_id}")
async def get_habit(
    habit_id: Annotated[int, Path(ge=1, description="Habit ID")],
    include_stats: Annotated[bool, Query()] = False,
):
    pass
```

- **Path parameters**: resource identification (`/habits/123`)
- **Query parameters**: filtering, sorting, pagination (`?limit=10&offset=0`)

### Response Models and Status Codes

```python
@router.post("/", response_model=HabitResponse, status_code=status.HTTP_201_CREATED)
async def create_habit(habit: HabitCreate):
    return created_habit

@router.delete("/{habit_id}", status_code=status.HTTP_204_NO_CONTENT)
async def delete_habit(habit_id: int):
    return None  # 204 has no body

@router.get("/{habit_id}")
async def get_habit(habit_id: int):
    if not habit:
        raise HTTPException(status_code=404, detail="Habit not found")
    return habit
```

---

## 3. Pydantic Models & Validation

### Base Schema Pattern

```python
from pydantic import BaseModel, ConfigDict

class BaseSchema(BaseModel):
    model_config = ConfigDict(from_attributes=True)  # ORM compatibility
```

### Request/Response Schema Pattern

```python
from pydantic import BaseModel, Field
from datetime import datetime

class HabitBase(BaseModel):
    name: str = Field(..., min_length=1, max_length=100)
    description: str | None = None

class HabitCreate(HabitBase):
    color: str = Field(default="#10B981", pattern=r"^#[0-9A-Fa-f]{6}$")

class HabitUpdate(BaseModel):
    name: str | None = Field(None, min_length=1, max_length=100)
    description: str | None = None
    color: str | None = Field(None, pattern=r"^#[0-9A-Fa-f]{6}$")

class HabitResponse(HabitBase):
    id: int
    color: str
    created_at: datetime

    model_config = ConfigDict(from_attributes=True)
```

### Field Validation

```python
from pydantic import field_validator, model_validator

class HabitCreate(BaseModel):
    name: str = Field(..., min_length=1, max_length=100)

    @field_validator("name")
    @classmethod
    def name_must_not_be_empty(cls, v: str) -> str:
        if not v.strip():
            raise ValueError("Name cannot be blank")
        return v.strip()

    @model_validator(mode="after")
    def check_consistency(self):
        # Cross-field validation
        return self
```

### Nested Models

```python
class CompletionResponse(BaseModel):
    date: str
    status: str

class HabitDetailResponse(HabitResponse):
    completions: list[CompletionResponse]
```

---

## 4. Dependency Injection

### Database Dependency

```python
from fastapi import Depends
from sqlalchemy.orm import Session

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

@router.get("/habits")
def list_habits(db: Session = Depends(get_db)):
    return db.query(Habit).all()
```

### Validation Dependencies

```python
async def valid_habit_id(habit_id: int, db: Session = Depends(get_db)) -> Habit:
    habit = db.query(Habit).filter(Habit.id == habit_id).first()
    if not habit:
        raise HTTPException(status_code=404, detail="Habit not found")
    return habit

@router.get("/habits/{habit_id}")
async def get_habit(habit: Habit = Depends(valid_habit_id)):
    return habit  # Already validated and fetched

@router.delete("/habits/{habit_id}")
async def delete_habit(habit: Habit = Depends(valid_habit_id), db: Session = Depends(get_db)):
    db.delete(habit)
    db.commit()
```

### Chained Dependencies

```python
async def get_current_user(token: str = Depends(oauth2_scheme)) -> User:
    return user

async def get_current_active_user(user: User = Depends(get_current_user)) -> User:
    if not user.is_active:
        raise HTTPException(status_code=400, detail="Inactive user")
    return user

@router.get("/me")
async def read_current_user(user: User = Depends(get_current_active_user)):
    return user
```

### Pagination Dependency

```python
class Pagination:
    def __init__(self, skip: int = 0, limit: int = Query(default=100, le=100)):
        self.skip = skip
        self.limit = limit

@router.get("/habits")
async def list_habits(pagination: Pagination = Depends()):
    return habits[pagination.skip : pagination.skip + pagination.limit]
```

---

## 5. Error Handling

### HTTPException

```python
from fastapi import HTTPException, status

@router.get("/habits/{habit_id}")
async def get_habit(habit_id: int):
    habit = get_habit_by_id(habit_id)
    if not habit:
        raise HTTPException(
            status_code=status.HTTP_404_NOT_FOUND,
            detail="Habit not found",
        )
    return habit
```

### Custom Exception Classes

```python
class AppException(Exception):
    def __init__(self, status_code: int, detail: str, error_code: str):
        self.status_code = status_code
        self.detail = detail
        self.error_code = error_code

class NotFoundError(AppException):
    def __init__(self, resource: str, resource_id: int):
        super().__init__(
            status_code=404,
            detail=f"{resource} with ID {resource_id} not found",
            error_code=f"{resource.upper()}_NOT_FOUND",
        )

class DuplicateError(AppException):
    def __init__(self, detail: str):
        super().__init__(
            status_code=409,
            detail=detail,
            error_code="DUPLICATE_RESOURCE",
        )
```

### Global Exception Handler

```python
from fastapi import Request
from fastapi.responses import JSONResponse

@app.exception_handler(AppException)
async def app_exception_handler(request: Request, exc: AppException):
    return JSONResponse(
        status_code=exc.status_code,
        content={
            "error": exc.error_code,
            "detail": exc.detail,
        },
    )
```

---

## 6. Database Integration

### SQLAlchemy Setup (PostgreSQL/Supabase)

```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker, declarative_base
import os

DATABASE_URL = os.getenv("DATABASE_URL")
engine = create_engine(DATABASE_URL)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
Base = declarative_base()
```

### Model Definition

```python
from sqlalchemy import Column, Integer, String, Text, DateTime, ForeignKey, UniqueConstraint
from sqlalchemy.orm import relationship
from datetime import datetime

class Habit(Base):
    __tablename__ = "habits"

    id = Column(Integer, primary_key=True, index=True)
    name = Column(String(100), nullable=False)
    description = Column(Text)
    color = Column(String(7), default="#10B981")
    created_at = Column(DateTime, default=datetime.utcnow)

    completions = relationship(
        "Completion",
        back_populates="habit",
        cascade="all, delete-orphan",
    )

class Completion(Base):
    __tablename__ = "completions"

    id = Column(Integer, primary_key=True, index=True)
    habit_id = Column(Integer, ForeignKey("habits.id", ondelete="CASCADE"), nullable=False)
    completed_date = Column(String(10), nullable=False)

    habit = relationship("Habit", back_populates="completions")

    __table_args__ = (
        UniqueConstraint("habit_id", "completed_date", name="uq_habit_date"),
    )
```

### Database Dependency

```python
from typing import Generator

def get_db() -> Generator[Session, None, None]:
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```

### Eager Loading

```python
from sqlalchemy.orm import joinedload, selectinload

# For many-to-one
habit = db.query(Habit).options(joinedload(Habit.category)).first()

# For one-to-many
habits = db.query(Habit).options(selectinload(Habit.completions)).all()
```

---

## 7. Performance & Async

### Async vs Sync

| Work Type | Function | Reason |
|-----------|----------|--------|
| Async I/O (DB, HTTP) | `async def` | Non-blocking |
| Sync/blocking I/O | `def` | Runs in threadpool |
| CPU-bound | External worker | Avoids blocking |

```python
# GOOD: Sync for blocking (FastAPI handles threadpool)
@router.get("/file")
def read_file():
    with open("file.txt") as f:
        return f.read()

# BAD: Blocking in async
@router.get("/bad")
async def bad_endpoint():
    time.sleep(5)  # Blocks entire event loop!
```

### Background Tasks

```python
from fastapi import BackgroundTasks

def send_notification(email: str, message: str):
    pass

@router.post("/habits")
async def create_habit(habit: HabitCreate, background_tasks: BackgroundTasks):
    created = create_habit_in_db(habit)
    background_tasks.add_task(send_notification, "user@example.com", "Habit created!")
    return created
```

---

## 8. Testing

### Test Setup

```python
# tests/conftest.py
import pytest
from fastapi.testclient import TestClient
from sqlalchemy import create_engine, StaticPool
from sqlalchemy.orm import sessionmaker

from app.main import app
from app.database import Base, get_db

@pytest.fixture(name="session")
def session_fixture():
    engine = create_engine(
        "sqlite:///:memory:",
        connect_args={"check_same_thread": False},
        poolclass=StaticPool,
    )
    Base.metadata.create_all(engine)
    SessionLocal = sessionmaker(bind=engine)
    with SessionLocal() as session:
        yield session

@pytest.fixture(name="client")
def client_fixture(session):
    def get_session_override():
        return session

    app.dependency_overrides[get_db] = get_session_override
    client = TestClient(app)
    yield client
    app.dependency_overrides.clear()
```

### Writing Tests

```python
def test_create_habit(client):
    response = client.post(
        "/api/habits",
        json={"name": "Exercise", "description": "Daily workout"},
    )
    assert response.status_code == 201
    assert response.json()["name"] == "Exercise"

def test_create_habit_validation_error(client):
    response = client.post("/api/habits", json={"name": ""})
    assert response.status_code == 422

def test_get_habit_not_found(client):
    response = client.get("/api/habits/999")
    assert response.status_code == 404
```

---

## 9. Security

### CORS Configuration

```python
from fastapi.middleware.cors import CORSMiddleware
from .config import get_settings

settings = get_settings()

app.add_middleware(
    CORSMiddleware,
    allow_origins=settings.cors_origins,
    allow_credentials=True,
    allow_methods=["GET", "POST", "PUT", "DELETE"],
    allow_headers=["Authorization", "Content-Type"],
)
```

### SQL Injection Prevention

```python
# GOOD: Use ORM
habit = db.query(Habit).filter(Habit.id == habit_id).first()

# GOOD: Parameterized queries
result = db.execute(text("SELECT * FROM habits WHERE id = :id"), {"id": habit_id})

# BAD: String formatting (VULNERABLE!)
db.execute(f"SELECT * FROM habits WHERE id = {habit_id}")
```

### Rate Limiting

```python
from slowapi import Limiter
from slowapi.util import get_remote_address

limiter = Limiter(key_func=get_remote_address)

@app.get("/api/habits")
@limiter.limit("100/minute")
async def list_habits(request: Request):
    pass
```

---

## 10. Configuration

### Pydantic Settings

```python
from pydantic_settings import BaseSettings, SettingsConfigDict
from functools import lru_cache

class Settings(BaseSettings):
    app_name: str = "My App"
    database_url: str
    debug: bool = False
    cors_origins: list[str] = ["http://localhost:3000"]

    model_config = SettingsConfigDict(
        env_file=".env",
        env_file_encoding="utf-8",
    )

@lru_cache
def get_settings() -> Settings:
    return Settings()
```

---

## 11. Anti-Patterns

| Anti-Pattern | Problem | Solution |
|--------------|---------|----------|
| Blocking I/O in `async def` | Halts event loop | Use async libs or plain `def` |
| Endpoint-to-endpoint calls | Tight coupling | Use service layer |
| Returning ORM objects directly | Exposes internals | Use response schemas |
| Not using Pydantic | Missing validation | Always define schemas |
| String-formatted SQL | SQL injection | Use ORM/parameterized queries |
| Hardcoded config | Inflexible | Use environment variables |
| Global mutable state | Race conditions | Use database |

```python
# BAD: Not closing connections
def bad_db():
    return SessionLocal()

# BAD: Sharing session between requests
db = SessionLocal()

# BAD: Exposing internal model
@router.get("/habits")
def list_habits(db: Session = Depends(get_db)):
    return db.query(Habit).all()

# GOOD: Use response model
@router.get("/habits", response_model=list[HabitResponse])
def list_habits(db: Session = Depends(get_db)):
    return db.query(Habit).all()
```

---

## Lifespan Events

```python
from contextlib import asynccontextmanager
from fastapi import FastAPI

@asynccontextmanager
async def lifespan(app: FastAPI):
    # Startup
    print("Starting up...")
    yield
    # Shutdown
    print("Shutting down...")

app = FastAPI(lifespan=lifespan)
```

---

## Quick Reference

### HTTP Status Codes

| Code | Use Case |
|------|----------|
| 200 | Successful GET, PUT |
| 201 | Successful POST (created) |
| 204 | Successful DELETE |
| 400 | Invalid request |
| 404 | Resource not found |
| 409 | Duplicate/conflict |
| 422 | Validation error |

### Common Imports

```python
from fastapi import FastAPI, APIRouter, Depends, HTTPException, status, Query, Path
from fastapi.middleware.cors import CORSMiddleware
from pydantic import BaseModel, Field, field_validator, ConfigDict
from sqlalchemy.orm import Session
from typing import Annotated
```

---

## Resources

- [FastAPI Documentation](https://fastapi.tiangolo.com/)
- [Pydantic Documentation](https://docs.pydantic.dev/)
- [SQLAlchemy 2.0 Documentation](https://docs.sqlalchemy.org/en/20/)
- [FastAPI Best Practices (GitHub)](https://github.com/zhanymkanov/fastapi-best-practices)
