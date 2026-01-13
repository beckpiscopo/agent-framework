# Testing Reference

> Testing patterns and conventions for this project

## Philosophy

- Test behavior, not implementation
- Prioritize integration tests for API endpoints
- Unit test complex business logic
- E2E tests for critical user flows

---

## Backend Testing (pytest)

### Project Structure
```
backend/
├── tests/
│   ├── conftest.py          # Shared fixtures
│   ├── test_auth.py         # Auth endpoint tests
│   ├── test_users.py        # User endpoint tests
│   └── unit/
│       └── test_services.py # Unit tests for services
```

### Fixtures (conftest.py)
```python
import pytest
from fastapi.testclient import TestClient
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from app.main import app
from app.database import Base, get_db

# Use SQLite for tests (fast, no setup needed)
SQLALCHEMY_DATABASE_URL = "sqlite:///./test.db"
engine = create_engine(SQLALCHEMY_DATABASE_URL, connect_args={"check_same_thread": False})
TestingSessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

@pytest.fixture(scope="function")
def db():
    Base.metadata.create_all(bind=engine)
    db = TestingSessionLocal()
    try:
        yield db
    finally:
        db.close()
        Base.metadata.drop_all(bind=engine)

@pytest.fixture(scope="function")
def client(db):
    def override_get_db():
        try:
            yield db
        finally:
            pass

    app.dependency_overrides[get_db] = override_get_db
    with TestClient(app) as c:
        yield c
    app.dependency_overrides.clear()

@pytest.fixture
def auth_headers(client):
    """Create a user and return auth headers."""
    # Register user
    client.post("/api/auth/register", json={
        "email": "test@example.com",
        "password": "testpassword123"
    })

    # Login
    response = client.post("/api/auth/login", json={
        "email": "test@example.com",
        "password": "testpassword123"
    })
    token = response.json()["access_token"]

    return {"Authorization": f"Bearer {token}"}
```

### Integration Tests
```python
# tests/test_auth.py

def test_register_success(client):
    response = client.post("/api/auth/register", json={
        "email": "new@example.com",
        "password": "password123"
    })
    assert response.status_code == 201
    assert "id" in response.json()
    assert response.json()["email"] == "new@example.com"

def test_register_duplicate_email(client):
    # First registration
    client.post("/api/auth/register", json={
        "email": "dupe@example.com",
        "password": "password123"
    })

    # Duplicate registration
    response = client.post("/api/auth/register", json={
        "email": "dupe@example.com",
        "password": "password123"
    })
    assert response.status_code == 400
    assert "already registered" in response.json()["detail"]

def test_login_success(client):
    # Register first
    client.post("/api/auth/register", json={
        "email": "login@example.com",
        "password": "password123"
    })

    # Login
    response = client.post("/api/auth/login", json={
        "email": "login@example.com",
        "password": "password123"
    })
    assert response.status_code == 200
    assert "access_token" in response.json()

def test_protected_route_requires_auth(client):
    response = client.get("/api/users/me")
    assert response.status_code == 401

def test_protected_route_with_auth(client, auth_headers):
    response = client.get("/api/users/me", headers=auth_headers)
    assert response.status_code == 200
    assert response.json()["email"] == "test@example.com"
```

### Running Tests
```bash
# Run all tests
pytest

# Run with coverage
pytest --cov=app --cov-report=html

# Run specific file
pytest tests/test_auth.py

# Run specific test
pytest tests/test_auth.py::test_login_success

# Verbose output
pytest -v
```

---

## Frontend Testing (Vitest + Testing Library)

### Project Structure
```
frontend/
├── src/
│   ├── components/
│   │   └── ui/
│   │       ├── button.tsx
│   │       └── button.test.tsx    # Co-located tests
│   └── hooks/
│       ├── use-auth.ts
│       └── use-auth.test.ts
├── tests/
│   └── e2e/                       # Playwright E2E tests
│       └── auth.spec.ts
```

### Component Tests
```tsx
// components/ui/button.test.tsx
import { render, screen } from "@testing-library/react"
import userEvent from "@testing-library/user-event"
import { describe, it, expect, vi } from "vitest"
import { Button } from "./button"

describe("Button", () => {
  it("renders children", () => {
    render(<Button>Click me</Button>)
    expect(screen.getByRole("button")).toHaveTextContent("Click me")
  })

  it("calls onClick when clicked", async () => {
    const onClick = vi.fn()
    render(<Button onClick={onClick}>Click me</Button>)

    await userEvent.click(screen.getByRole("button"))

    expect(onClick).toHaveBeenCalledTimes(1)
  })

  it("is disabled when disabled prop is true", () => {
    render(<Button disabled>Click me</Button>)
    expect(screen.getByRole("button")).toBeDisabled()
  })

  it("applies variant styles", () => {
    render(<Button variant="secondary">Click me</Button>)
    expect(screen.getByRole("button")).toHaveClass("bg-gray-200")
  })
})
```

### Hook Tests
```tsx
// hooks/use-auth.test.ts
import { renderHook, waitFor } from "@testing-library/react"
import { QueryClient, QueryClientProvider } from "@tanstack/react-query"
import { describe, it, expect, vi } from "vitest"
import { useUser } from "./use-user"

// Mock fetch
global.fetch = vi.fn()

const wrapper = ({ children }) => {
  const client = new QueryClient({
    defaultOptions: { queries: { retry: false } },
  })
  return <QueryClientProvider client={client}>{children}</QueryClientProvider>
}

describe("useUser", () => {
  it("fetches current user", async () => {
    const mockUser = { id: "1", email: "test@example.com" }
    vi.mocked(fetch).mockResolvedValueOnce({
      ok: true,
      json: async () => mockUser,
    } as Response)

    const { result } = renderHook(() => useUser(), { wrapper })

    await waitFor(() => expect(result.current.isSuccess).toBe(true))
    expect(result.current.data).toEqual(mockUser)
  })
})
```

### Running Frontend Tests
```bash
# Run tests
npm test

# Run with coverage
npm test -- --coverage

# Watch mode
npm test -- --watch
```

---

## E2E Testing (Playwright)

### Setup
```typescript
// playwright.config.ts
import { defineConfig } from "@playwright/test"

export default defineConfig({
  testDir: "./tests/e2e",
  use: {
    baseURL: "http://localhost:3000",
  },
  webServer: {
    command: "npm run dev",
    port: 3000,
  },
})
```

### E2E Test
```typescript
// tests/e2e/auth.spec.ts
import { test, expect } from "@playwright/test"

test.describe("Authentication", () => {
  test("user can register and login", async ({ page }) => {
    // Go to register page
    await page.goto("/register")

    // Fill form
    await page.fill('input[name="email"]', "e2e@example.com")
    await page.fill('input[name="password"]', "password123")

    // Submit
    await page.click('button[type="submit"]')

    // Should redirect to dashboard
    await expect(page).toHaveURL("/dashboard")
  })

  test("shows error for invalid login", async ({ page }) => {
    await page.goto("/login")

    await page.fill('input[name="email"]', "wrong@example.com")
    await page.fill('input[name="password"]', "wrongpassword")
    await page.click('button[type="submit"]')

    await expect(page.locator(".error-message")).toBeVisible()
  })
})
```

### Running E2E Tests
```bash
# Run all E2E tests
npx playwright test

# Run with UI
npx playwright test --ui

# Run specific file
npx playwright test tests/e2e/auth.spec.ts
```

---

## Coverage Requirements

| Type | Minimum Coverage |
|------|-----------------|
| Backend | 80% |
| Frontend Components | 70% |
| Critical Paths | 100% |

Focus coverage on:
- Business logic (services)
- API endpoints
- Form validation
- Auth flows
