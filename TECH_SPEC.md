
# TECHNICAL SPECIFICATION & CODING STANDARDS (TECH_SPEC)

## 1. Project Overview
Personal Finance Application designed for high-performance, mobile-first usage. The architecture isolates layout responsibilities from business logic.

## 2. Tech Stack Core
* **Framework:** Next.js 14+ (App Router)
* **Language:** TypeScript (Strict mode)
* **Styling:** Tailwind CSS (Utility-first)
* **UI Primitives:** Radix UI / Shadcn UI
* **Icons:** Lucide React
* **State Management:** Zustand (for global UI state like Sidebar/Theme)
* **Forms:** React Hook Form + Zod validation
* **Navigation Feedback:** Nextjs-toploader or nProgress

---

## 3. Directory Structure
[cite_start]Adhere strictly to this folder structure[cite: 39]:
```text
/src
  /app              # Next.js App Router pages
  /components
    /layout         # AppShell, Sidebar, Header, PageHeader
    /ui             # Generic primitives (Buttons, Inputs - Shadcn)
    /features       # Business logic components (e.g., TransactionForm)
  /lib              # Utilities, constants
  /hooks            # Custom React hooks
  /providers        # Context providers (Theme, Query, Auth)

```

---

## 4. Global Layout (AppShell)

The AppShell is the persistent visual structure. It defines WHERE elements appear, not WHAT appears.

### 4.1. Layout Geometry & Viewport

* 
**Height Rule:** The main container and sidebar MUST use `100dvh` (Dynamic Viewport Height) to prevent cut-off content on mobile browsers (Safari/Chrome Android).


* **Composition:**
1. **Sidebar:** Left-aligned (Desktop) / Drawer (Mobile).
2. **Header:** Sticky/Fixed at the top.
3. 
**Loading Bar:** Extreme top (Z-Index 100), visual only during route transitions.


4. 
**Main Content:** Scrollable area independent of Header/Sidebar.





### 4.2. Sidebar (Navigation) behavior

* **Desktop:** Fixed sidebar. Collapsible.
* 
**Mobile:** Off-canvas Drawer (Overlay).


* **Persistence:** The collapsed/expanded state MUST be saved in `localStorage`. This prevents "Layout Shift" when the user refreshes the page.


* **Active State:** Current route must be visually highlighted.

### 4.3. Header & Page Header

* 
**Global Header:** Contains Mobile Menu Trigger, Global Search (visual only), and User Profile.


* 
**Page Header:** A standard component rendered inside the Main Content area.


* **MUST** render: Breadcrumbs + H1 Page Title.
* **MUST NOT** render: Page actions (buttons, filters). These belong to the page body.





---

## 5. Design System & Tokens

The application uses a semantic token system. NEVER use hardcoded colors.

### 5.1. Z-Index Scale (Strict Layers)

Follow this table to avoid stacking context conflicts:
| Layer | Value | Usage |
| :--- | :--- | :--- |
| **Base** | `z-0` | Standard Content |
| **Sticky** | `z-10` | Sticky Headers / Filters |
| **Drawer** | `z-40` | Mobile Sidebar / Overlays |
| **Modal** | `z-50` | Dialogs / Popups |
| **Toast** | `z-100` | Notifications / Loading Bar |

### 5.2. Theme & Colors

* 
**Provider:** Use `next-themes` for Light/Dark mode support.


* 
**Usage:** Use Tailwind semantic classes (e.g., `bg-background`, `text-foreground`, `border-border`) instead of primitives (`bg-white`, `text-black`).



---

## 6. Coding Rules for Agents (AI Instructions)

### 6.1. General Principles

* 
**Mobile-First:** Always write CSS classes assuming mobile first, then add `sm:`, `md:`, `lg:` prefixes.


* **Clean Code:** Use meaningful variable names in English.
* **Components:** Prefer Server Components by default. Use `"use client"` only when interaction (hooks, event listeners) is strictly needed.

### 6.2. Functionality Constraints

* **No "Magic" Slots:** Do not create complex layout slots. Use standard React `children` prop.
* 
**Loading Feedback:** Ensure `nProgress` triggers automatically on route changes.


* 
**Scroll Restoration:** Ensure scroll position is preserved when navigating back to lists.



---

## 7. Accessibility (A11y)

* 
**Focus Management:** Ensure focus trap when Mobile Drawer or Modals are open.


* **Keyboard Nav:** Sidebar and Header must be fully navigable via keyboard.
* 
**Semantics:** Use proper HTML5 tags (`<nav>`, `<main>`, `<header>`, `<aside>`).



```

---

### Como usar este arquivo com os Agentes

Agora, sempre que você iniciar uma tarefa com a IA (seja no ChatGPT, Claude ou Cursor), sua **primeira mensagem** deve ser:

> "Vou desenvolver uma funcionalidade para meu App de Finanças.
>
> **Regra Primária:** Leia e siga estritamente o arquivo `TECH_SPEC.md` que estou enviando abaixo para garantir consistência de layout, z-index e estrutura de pastas. Não invente padrões novos.
>
> [Colar conteúdo do TECH_SPEC.md aqui ou anexar o arquivo]
>
> **Tarefa de hoje:** [Descreva a funcionalidade...]"

Isso cria o "trilho" que garante a assertividade que você procura. Quer que eu detalhe o primeiro prompt para criar a estrutura inicial (scaffolding) usando esse spec?

```
