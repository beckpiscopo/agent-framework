# Backend/API Reference

> FastAPI patterns and conventions for this project

## Project Structure

```
backend/
├── app/
│   ├── __init__.py
│   ├── main.py              # FastAPI app entry point
│   ├── config.py            # Settings and environment
│   ├── database.py          # Database connection
│   ├── dependencies.py      # Shared dependencies
│   │
│   ├── models/              # SQLAlchemy models
│   │   ├── __init__.py
│   │   ├── user.py
│   │   └── ...
│   │
│   ├── schemas/             # Pydantic schemas
│   │   ├── __init__.py
│   │   ├── user.py
│   │   └── ...
│   │
│   ├── routers/             # API route handlers
│   │   ├── __init__.py
│   │   ├── auth.py
│   │   ├── users.py
│   │   └── ...
│   │
│   ├── services/            # Business logic
│   │   ├── __init__.py
│   │   ├── auth_service.py
│   │   └── ...
│   │
│   └── utils/               # Helper functions
│       ├── __init__.py
│       └── ...
│
├── alembic/                 # Database migrations
├── tests/                   # Test files
├── requirements.txt
└── pyproject.toml
```

---

## Conventions

### Naming
- **Files:** `snake_case.py`
- **Classes:** `PascalCase`
- **Functions/Variables:** `snake_case`
- **Constants:** `SCREAMING_SNAKE_CASE`
- **API Endpoints:** `kebab-case` in URLs

### Models (SQLAlchemy)
```python
from sqlalchemy import Column, String, DateTime
from sqlalchemy.dialects.postgresql import UUID
from app.database import Base
import uuid
from datetime import datetime

class User(Base):
    __tablename__ = "users"

    id = Column(UUID(as_uuid=True), primary_key=True, default=uuid.uuid4)
    email = Column(String, unique=True, index=True, nullable=False)
    password_hash = Column(String, nullable=False)
    created_at = Column(DateTime, default=datetime.utcnow)
    updated_at = Column(DateTime, default=datetime.utcnow, onupdate=datetime.utcnow)
```

### Schemas (Pydantic)
```python
from pydantic import BaseModel, EmailStr
from uuid import UUID
from datetime import datetime

# Input schema (what client sends)
class UserCreate(BaseModel):
    email: EmailStr
    password: str

# Output schema (what API returns)
class UserResponse(BaseModel):
    id: UUID
    email: EmailStr
    created_at: datetime

    class Config:
        from_attributes = True
```

### Routers
```python
from fastapi import APIRouter, Depends, HTTPException, status
from sqlalchemy.orm import Session
from app.dependencies import get_db
from app.schemas.user import UserCreate, UserResponse
from app.services import user_service

router = APIRouter(prefix="/users", tags=["users"])

@router.post("/", response_model=UserResponse, status_code=status.HTTP_201_CREATED)
def create_user(user_in: UserCreate, db: Session = Depends(get_db)):
    existing = user_service.get_by_email(db, user_in.email)
    if existing:
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail="Email already registered"
        )
    return user_service.create(db, user_in)
```

### Services (Business Logic)
```python
from sqlalchemy.orm import Session
from app.models.user import User
from app.schemas.user import UserCreate
from app.utils.security import hash_password

def get_by_email(db: Session, email: str) -> User | None:
    return db.query(User).filter(User.email == email).first()

def create(db: Session, user_in: UserCreate) -> User:
    user = User(
        email=user_in.email,
        password_hash=hash_password(user_in.password)
    )
    db.add(user)
    db.commit()
    db.refresh(user)
    return user
```

---

## Error Handling

Use HTTPException with appropriate status codes:

```python
from fastapi import HTTPException, status

# 400 - Bad Request (validation, business logic)
raise HTTPException(status_code=400, detail="Invalid input")

# 401 - Unauthorized (not authenticated)
raise HTTPException(status_code=401, detail="Not authenticated")

# 403 - Forbidden (authenticated but not allowed)
raise HTTPException(status_code=403, detail="Not enough permissions")

# 404 - Not Found
raise HTTPException(status_code=404, detail="Resource not found")

# 409 - Conflict (duplicate, etc)
raise HTTPException(status_code=409, detail="Resource already exists")
```

---

## Authentication Pattern

```python
from fastapi import Depends
from fastapi.security import HTTPBearer, HTTPAuthorizationCredentials
from app.utils.jwt import decode_token

security = HTTPBearer()

def get_current_user(
    credentials: HTTPAuthorizationCredentials = Depends(security),
    db: Session = Depends(get_db)
) -> User:
    token = credentials.credentials
    payload = decode_token(token)
    if not payload:
        raise HTTPException(status_code=401, detail="Invalid token")

    user = db.query(User).filter(User.id == payload["sub"]).first()
    if not user:
        raise HTTPException(status_code=401, detail="User not found")

    return user
```

---

## Database Migrations (Alembic)

```bash
# Create a new migration
alembic revision --autogenerate -m "description"

# Run migrations
alembic upgrade head

# Rollback one migration
alembic downgrade -1
```

---

## Environment Variables

Required in `.env`:
```
DATABASE_URL=postgresql://user:pass@localhost:5432/dbname
SECRET_KEY=your-secret-key-here
ACCESS_TOKEN_EXPIRE_MINUTES=30
```
