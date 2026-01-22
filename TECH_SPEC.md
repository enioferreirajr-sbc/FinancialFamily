Aqui está o arquivo `TECH_SPEC.md` completo e consolidado com todas as atualizações (Backend Python/FastAPI, MongoDB/Beanie e padrões REST).

Salve este arquivo na raiz do seu projeto. Ele será a "Bíblia" para os Agentes de IA.

```markdown
# TECHNICAL SPECIFICATION & CODING STANDARDS (TECH_SPEC)

## 1. Project Overview
High-performance application designed for mobile-first usage. The architecture decouples the high-interactivity frontend (Next.js) from a robust, modular backend (FastAPI/Python).

## 2. Tech Stack Core

### 2.1. Frontend (Client)
* **Framework:** Next.js 14+ (App Router)
* **Language:** TypeScript (Strict mode)
* **Styling:** Tailwind CSS (Utility-first)
* **UI Primitives:** Radix UI / Shadcn UI
* **Icons:** Lucide React
* **State Management:** Zustand (Global UI state), TanStack Query (Server state/Caching)
* **Forms:** React Hook Form + Zod validation

### 2.2. Backend (Server)
* **Language:** Python 3.11+
* **Framework:** FastAPI
* **Server:** Uvicorn (Standard)
* **Linter:** Ruff (Strict configuration, enforcing `snake_case`)
* **Architecture:** Modular Monolith (Layered Architecture)

### 2.3. Database (NoSQL)
* **Engine:** MongoDB
* **Driver:** Motor (Async driver)
* **ODM (Object Document Mapper):** Beanie
    * *Why:* Allows using Pydantic models directly as Database Documents.
* **Naming Convention:**
    * Collections: `snake_case` (e.g., `users`, `transaction_logs`).
    * Fields: `snake_case` in Python/DB.

---

## 3. Directory Structure
Adhere strictly to this folder structure to maintain separation of concerns.

### 3.1 Frontend Structure (/frontend)
```text
/src
  /app              # Next.js App Router pages
  /components
    /layout         # AppShell, Sidebar, Header (Shell only)
    /ui             # Generic primitives (Buttons, Inputs - Shadcn)
    /features       # Domain components (e.g., TransactionForm, DashboardCharts)
  /lib              # Utilities (axios instance, formatting)
  /hooks            # Custom React hooks
  /services         # API calls functions (repositories)

```

### 3.2 Backend Structure (/backend)

```text
/app
  /core             # Project configuration
    config.py       # Environment variables (Pydantic Settings)
    database.py     # MongoDB connection & Beanie initialization
    exceptions.py   # Global Exception Handlers
    security.py     # JWT & Password hashing utils

  /models           # Database Models (Beanie/MongoDB Documents)
    user_model.py
    # Models represent exactly what is stored in MongoDB

  /schemas          # Pydantic Schemas (Input/Output Data Transfer Objects)
    user_schema.py      # UserCreate, UserResponse, UserUpdate
    # Schemas validate what enters and leaves the API

  /services         # Business Logic (The "Brain")
    # Agents must put complex logic here, NEVER in the router.
    auth_service.py
    user_service.py

  /api
    /v1
      api.py            # Router Aggregator (include_router)
      /endpoints        # Route Controllers (receive request -> call service -> return response)
        auth.py
        users.py

  main.py           # App Entrypoint (FastAPI app init, CORS, Middleware)

```

---

## 4. Global Layout (AppShell) - Frontend

The AppShell is the persistent visual structure. It defines WHERE elements appear, not WHAT appears.

### 4.1. Layout Geometry & Viewport

* **Height Rule:** The main container and sidebar MUST use `100dvh` (Dynamic Viewport Height) to prevent cut-off content on mobile browsers (Safari/Chrome Android).
* **Composition:**
1. **Sidebar:** Left-aligned (Desktop) / Drawer (Mobile).
2. **Header:** Sticky/Fixed at the top.
3. **Loading Bar:** Extreme top (Z-Index 100), visual only during route transitions.
4. **Main Content:** Scrollable area independent of Header/Sidebar.



### 4.2. Sidebar (Navigation) behavior

* **Desktop:** Fixed sidebar. Collapsible.
* **Mobile:** Off-canvas Drawer (Overlay).
* **Persistence:** The collapsed/expanded state MUST be saved in `localStorage`.
* **Active State:** Current route must be visually highlighted.

---

## 5. Design System & Tokens

The application uses a semantic token system. NEVER use hardcoded colors.

### 5.1. Z-Index Scale (Strict Layers)

| Layer | Value | Usage |
| --- | --- | --- |
| **Base** | `z-0` | Standard Content |
| **Sticky** | `z-10` | Sticky Headers / Filters |
| **Drawer** | `z-40` | Mobile Sidebar / Overlays |
| **Modal** | `z-50` | Dialogs / Popups |
| **Toast** | `z-100` | Notifications / Loading Bar |

### 5.2. Theme

* **Provider:** `next-themes` (System/Dark/Light).
* **Usage:** Use Tailwind semantic classes (e.g., `bg-background`, `text-foreground`, `border-border`) instead of primitives.

---

## 6. Coding Rules for Agents (AI Instructions)

### 6.1. General Principles

* **Mobile-First:** Always write CSS classes assuming mobile first (`w-full`), then add breakpoints (`md:w-1/2`).
* **Explicit Typing:** No `any`. Use generic types for API responses.
* **Clean Code:** Meaningful English variable names.

### 6.2. Frontend Specifics

* **Server Components:** Default choice. Use `"use client"` only for interactivity.
* **Loading Feedback:** Ensure `nProgress` or Skeletons trigger on data fetch.
* **API Consumption:** Use the defined `src/services` functions, do not fetch directly inside components.

### 6.3. Backend Specifics (Python)

* **Async/Await:** All DB calls (Beanie) must be asynchronous.
* **Dependency Injection:** Use FastAPI `Depends` for Services.
* **Docstrings:** All endpoints must have a description for Swagger generation.
* **Naming:** Use `snake_case` for everything in Python. Pydantic will handle the conversion to JSON `camelCase` automatically via `alias_generator` if configured, or just keep consistent JSON keys.

---

## 7. API Standards & Error Handling

### 7.1. REST Conventions

* **URL Structure:** `/api/v1/{resource}`.
* **Methods:**
* `GET`: Retrieve data (Safe, Idempotent).
* `POST`: Create new resource (201 Created).
* `PUT`: Full update.
* `PATCH`: Partial update.
* `DELETE`: Remove resource (204 No Content).



### 7.2. Status Codes

* `200 OK`: Success (GET, PUT, PATCH).
* `201 Created`: Success (POST).
* `204 No Content`: Success (DELETE).
* `400 Bad Request`: Validation error or Business rule violation.
* `401 Unauthorized`: Missing or invalid token.
* `403 Forbidden`: Valid token but insufficient permissions.
* `404 Not Found`: Resource does not exist.
* `422 Unprocessable Entity`: Pydantic validation error (automatic).
* `500 Internal Server Error`: Unhandled crash.

### 7.3. Standard Error Response

All errors (except 500) must return a consistent JSON structure. Agents must use FastAPI `HTTPException`.

**Format:**

```json
{
  "detail": "Description of the error",
  "code": "ERROR_CODE_STRING"
}

```

**Implementation Guide for Agents:**

* Do not return plain strings.
* Use `raise HTTPException(status_code=404, detail="User not found")`.
* A global exception handler in `core/exceptions.py` must catch unhandled exceptions and return a safe 500 JSON to prevent leaking stack traces.

---

## 8. Accessibility (A11y)

* **Focus Management:** Ensure focus trap when Mobile Drawer or Modals are open.
* **Keyboard Nav:** Sidebar and Header must be fully navigable via keyboard.
* **Semantics:** Use proper HTML5 tags (`<nav>`, `<main>`, `<header>`, `<aside>`).

```

```
