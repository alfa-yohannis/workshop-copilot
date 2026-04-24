---
applyTo: "**"
---

# Copilot Workspace Instructions

Soc Ops is a **Social Bingo game** (FastAPI + Jinja2 + HTMX) for in-person mixers. Players find people matching questions to mark 5-in-a-row bingo squares.

---

## ✅ MANDATORY: Development Checklist

**Before committing ANY changes:**

```bash
uv run ruff check .           # Lint (fails if errors)
uv run pytest                 # Tests (all 25 must pass)
uv run uvicorn app.main:app --reload --host 0.0.0.0 --port 8000  # Dev server
```

**Code requirements:**
- [ ] Type hints on all functions
- [ ] snake_case naming convention
- [ ] No unused imports or variables
- [ ] 88-char line limit (enforced by ruff)

---

## ⚡ Quick Commands

```bash
uv sync                    # Install dependencies
uv run pytest -v           # Verbose test output
uv run pytest tests/test_game_logic.py  # Run single test file
curl http://localhost:8000 # Test server (when running)
```

---

## 📂 Project Structure

```
app/
├── main.py         # FastAPI routes & HTMX handlers
├── models.py       # Pydantic models (GameState, BingoSquareData, BingoLine)
├── game_logic.py   # Board generation & bingo detection
├── game_service.py # GameSession: state persistence via cookies
├── data.py         # Question pool
├── templates/base.html, home.html, components/*.html
└── static/css/app.css      # Custom utility classes

tests/
├── test_api.py          # HTTP endpoint tests (TestClient)
└── test_game_logic.py   # Game logic unit tests (25 tests)

.github/
├── instructions/   # css-utilities, frontend-design, general
└── agents/         # quiz-master, tdd, ui-review, pixel-jam
```

---

## 🏗️ Key Architecture

**State Management:**
- `GameSession` class manages server-side state
- Persisted via signed cookies (itsdangerous, tamper-proof)
- GameState enum: START → PLAYING → BINGO

**HTMX Flow:**
1. POST `/start` → returns `game_screen.html` fragment
2. POST `/toggle/{square_id}` → returns updated `game_screen.html`
3. Endpoints return HTML fragments, not JSON
4. No page reloads needed

**Type Safety:**
- Pydantic models with frozen configs (immutable)
- Python 3.13+ required (ruff enforces type hints)
- All functions decorated with type hints

---

## 🎨 Frontend

**CSS:**
- Custom utilities in `app/static/css/app.css` (no Tailwind)
- Use existing classes: `.flex`, `.grid`, `.grid-cols-5`, `.gap-2`, `.p-4`, `.bg-accent`, `.text-gray-700`
- Add new utilities to `app.css` following existing patterns

**Templates:**
- Jinja2 with inheritance from `base.html`
- Components as partials in `components/` folder
- Context: `request`, `session`, `GameState` enum passed to templates

See [css-utilities.instructions.md](instructions/css-utilities.instructions.md) and [frontend-design.instructions.md](instructions/frontend-design.instructions.md) for details.

---

## 🧭 Design Guide

**Theme & layout:**
- Keep the UX warm, bold, and playful for icebreaker events.
- Use strong color contrast for game state cues: action buttons, bingo alerts, and marked squares.
- Preserve clear hierarchy: header, instruction text, board, and modal.

**Visual style:**
- Favor rounded cards, soft shadows, and consistent spacing.
- Use large, readable text for board items and game states.
- Keep the board visually centered with a clean surrounding canvas.

**Component behavior:**
- Buttons should feel tactile through color changes and rounded shapes.
- Modal overlays should be prominent but not cluttered.
- Use HTML fragment responses for HTMX updates, not full page refreshes.

---

## 🔧 Common Tasks

| Task | How |
|------|-----|
| Add bingo question | Edit `app/data.py`, add to `QUESTIONS` list |
| Style component | Add CSS classes (or create new utility in `app.css`) |
| Create endpoint | Add to `app/main.py`, use `_get_game_session()`, return `TemplateResponse` |
| Add test | Create test in `tests/test_api.py` or `test_game_logic.py`, run `uv run pytest` |

---

## ⚠️ Gotchas

| Issue | Fix |
|-------|-----|
| Simple Browser used | Use real browser (`$BROWSER http://localhost:8000`) — HTMX requires full browser |
| Session lost on restart | Expected — sessions are in-memory, restart clears all games |
| HTMX not working | Check endpoint returns HTML fragment (not full page), verify `hx-target` / `hx-swap` |
| CSS class not found | Must exist in `app/static/css/app.css` — no Tailwind fallbacks |
| Type hint errors | Python 3.13+ required; ruff enforces type hints on public functions |

---

## 🔗 Learn More

- [css-utilities.instructions.md](instructions/css-utilities.instructions.md) — Utility class reference
- [frontend-design.instructions.md](instructions/frontend-design.instructions.md) — Design principles
- [general.instructions.md](instructions/general.instructions.md) — General guidelines
- Agents: `quiz-master`, `tdd`, `ui-review`, `pixel-jam` in `.github/agents/`
