# CircuitVerse GSoC 2026: Beginner Experience Improvements

This repository documents my preparation for contributing to **CircuitVerse** through Google Summer of Code 2026. The project focuses on three interconnected improvements to the CircuitVerse editor: a reusable **circuit template library**, a **guided learning workflow**, and **contextual onboarding help** — all designed to lower the barrier for beginners entering digital logic design.

---

## Why This Project

CircuitVerse is a powerful, browser-based digital logic simulator used by students, educators, and hobbyists worldwide[web:14]. It allows users to build, simulate, and share circuits entirely in the cloud, with features like an interactive schematic editor, timing diagrams, testbenches, and subcircuit support[web:34][web:47].

However, new users often hit a wall when they first open the editor. They are presented with a blank canvas and a large panel of circuit elements, but no guidance on what to do next. There is no built-in way to discover example circuits, no step-by-step tutorials integrated into the editor, and no contextual help that appears at the moment of need[web:34].

This project addresses that gap by making the editor itself more discoverable and learnable.

---

## The Problem: Current Beginner Experience

### What Beginners Face Today

| Pain Point | Current State | Impact |
|------------|---------------|--------|
| **Blank canvas anxiety** | No starter circuits or templates; users start from scratch | Beginners don't know where to begin |
| **No discoverable examples** | Examples exist in the Interactive Book[web:11] but are separate from the editor | Users must context-switch between book and simulator |
| **No guided progression** | No built-in tutorials or challenges within the editor | Learning curve is steep and self-directed |
| **No contextual help** | Tooltips are minimal; no empty-state guidance | Users get stuck without knowing where to find help |
| **No progress tracking** | No way to track learning milestones or completed exercises | No sense of accomplishment or structured path |

### Why Existing Features Fall Short

CircuitVerse already has several valuable features[web:34]:

- **Interactive schematic editor** — drag-and-drop circuit building on a canvas
- **Circuit elements panel** — logic gates, flip-flops, inputs, outputs, etc.
- **Properties panel** — edit parameters like bit width, clock time, orientation
- **Circuit tabs and subcircuits** — organize complex designs
- **Timing diagrams** — visualize signal behavior over clock cycles
- **Testbench** — verify circuit behavior with custom test vectors
- **Save/export to `.cv` format** — portable circuit files[web:21]
- **Project sharing** — collaborative features via the Rails backend

But none of these directly help a beginner **learn how to use them**. The features exist, but discoverability and onboarding are missing.

---

## Proposed Project Scope

### 1. Circuit Template Library

A curated, searchable library of starter circuits that beginners can browse, preview, and import directly into the editor.

**Technical Design:**
- **Data Model**: New `Template` model in PostgreSQL with fields: `name`, `description`, `circuit_json` (serialized circuit data), `category` (gates, adders, flip-flops, counters, etc.), `difficulty`, `tags`, `author_id`, `view_count`
- **Storage**: Circuit data stored as JSON in the `circuit_json` column, matching CircuitVerse's existing `.cv` save format[web:21]
- **API**: RESTful Rails endpoints: `GET /templates`, `GET /templates/:id`, `POST /templates`, `GET /templates/search?q=`
- **Frontend**: React component with filter chips, search bar, category navigation, and one-click import into the current canvas
- **Metadata schema**: Structured JSON with `metadata: { difficulty: "beginner", estimated_time: "10min", prerequisites: ["gates"], learning_outcomes: [...] }`

### 2. Guided Learning Workflow

An interactive tutorial system embedded in the editor that walks users through building circuits step by step.

**Technical Design:**
- **Data Model**: New `Tutorial` model with fields: `title`, `description`, `steps` (JSON array of step objects), `target_circuit_template_id`, `difficulty`, `estimated_duration`
- **Step schema**: Each step contains `{ instruction: string, expected_elements: [...], hint: string, validation_rule: function }`
- **Integration**: Tutorial panel docked alongside the editor canvas, showing current step with progress indicator
- **Progress tracking**: Store `TutorialProgress` records per user: `user_id`, `tutorial_id`, `current_step`, `completed_at`
- **React + Rails**: Frontend uses React components for the tutorial UI; Rails backend handles progress persistence via the existing authentication system

### 3. Contextual Help & Onboarding

Inline guidance that appears when the user needs it — not a separate documentation page.

**Technical Design:**
- **Empty-state detection**: React hooks that detect when the canvas is empty and display onboarding prompts
- **Contextual tooltips**: Enhanced tooltips using the existing CircuitVerse tooltip system, extended with "Learn More" links to templates/tutorials
- **Discoverability layer**: A help overlay (`?` icon) that highlights key UI regions: element panel, properties panel, circuit tabs, timing diagram
- **First-time user flow**: Flag `first_login` on User model triggers a guided tour on first editor visit
- **Help API**: `GET /help/context?element=AND_gate` returns contextual help snippets

---

## Existing Features vs Proposed Improvements

| Feature Area | Current State | After This Project |
|-------------|---------------|--------------------|
| **Example circuits** | None in editor; separate Interactive Book | Template library with 20+ starter circuits, searchable and importable |
| **Tutorials** | External documentation only | In-editor guided tutorials with step validation |
| **Onboarding** | No first-time guidance | Empty-state prompts + guided tour for new users |
| **Help system** | Basic tooltips | Contextual tooltips + help overlay + discovery layer |
| **Progress** | No tracking | Tutorial progress saved per user |
| **Import workflow** | `.cv` file upload only | One-click template import from library |

---

## User Flow Example

Here is how a beginner would experience the improved editor:

1. **Sign up** → Account created with `first_login: true`
2. **Open editor** → Empty-state onboarding appears: "Welcome! Start with a template, or explore the guided tutorial"
3. **Browse templates** → Click "Templates" tab → Filter by "Beginner" → Select "Half Adder"
4. **Import** → One click → Circuit loads onto canvas with explanation panel open
5. **Guided tutorial** → Click "Try Building This Yourself" → Step 1: "Add two input pins" → Validation runs as user drags elements
6. **Progress saved** → Step completed → Progress bar updates → Tutorial progress persisted to database
7. **Contextual help** → Hover over an element → Enhanced tooltip with "Learn more about XOR gates" link

---

## Technical Direction

The implementation works within CircuitVerse's existing technology stack:

| Layer | Technology | Notes |
|-------|-----------|-------|
| **Frontend** | React (via react-rails gem) | CircuitVerse uses React for the editor canvas and UI components[web:26] |
| **Backend** | Ruby on Rails 6+ | Existing Rails API; new models: `Template`, `Tutorial`, `TutorialProgress` |
| **Database** | PostgreSQL | New tables for templates and tutorials; circuit data stored as JSON |
| **Circuit Format** | `.cv` JSON | Reuse existing circuit serialization format[web:21] |
| **Testing** | Jest (frontend), RSpec (backend) | Follow existing CircuitVerse test patterns |

### Key Implementation Details

**Template import flow**:
- User clicks "Import" on a template → Frontend sends `POST /templates/:id/import`
- Rails controller loads circuit JSON → returns to React editor
- React editor deserializes JSON onto canvas (existing `.cv` import logic reused)

**Tutorial step validation**:
- Frontend maintains step state in React
- Each step has `expected_elements` array (e.g., `[{type: "Input", count: 2}, {type: "AND", count: 1}]`)
- On every canvas change, validation runs: if elements match expected, advance to next step
- Hint system: if user is stuck, click "Show Hint" for contextual guidance

**Progress tracking**:
- Rails API endpoint: `PATCH /tutorials/:id/progress` with `{ current_step: 3 }`
- `TutorialProgress` model: `belongs_to :user`, `belongs_to :tutorial`
- Dashboard page shows completed tutorials and in-progress work

---

## System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    CircuitVerse Editor (React)              │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐   │
│  │  Canvas  │  │  Element │  │ Template │  │ Tutorial │   │
│  │          │  │  Panel   │  │  Library │  │  Panel   │   │
│  │          │  │          │  │          │  │          │   │
│  │          │  │          │  │          │  │  Step    │   │
│  │          │  │          │  │ Search   │  │  Instr.  │   │
│  │          │  │          │  │ Filter   │  │  Hint    │   │
│  │          │  │          │  │ Preview  │  │ Progress │   │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘   │
└─────────────────────────────────────────────────────────────┘
                              │
                              │ REST API (Rails)
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    Rails Backend (PostgreSQL)               │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐                  │
│  │  User    │  │ Template │  │ Tutorial │                  │
│  │  Model   │  │  Model   │  │  Model   │                  │
│  │          │  │          │  │          │                  │
│  │ - id     │  │ - id     │  │ - id     │                  │
│  │ - email  │  │ - name   │  │ - title  │                  │
│  │ - ...    │  │ - json   │  │ - steps  │                  │
│  │          │  │ - tags   │  │ (JSON)   │                  │
│  │          │  │ - author │  │ - ...    │                  │
│  └──────────┘  └──────────┘  └──────────┘                  │
│  ┌────────────────────────────────────────────────────────┐ │
│  │              TutorialProgress                          │ │
│  │  - user_id  - tutorial_id  - current_step  - completed │ │
│  └────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
```

---

## 12-Week Implementation Plan

| Weeks | Phase | Focus | Deliverables |
|-------|-------|-------|-------------|
| **1–2** | Community Bonding | Codebase onboarding, dev env setup, schema design | Local setup working; ERD diagrams; API spec drafted; first PR merged |
| **3–4** | Phase 1 — Template Library (Backend) | `Template` model, migration, CRUD endpoints, search API | `GET /templates`, `POST /templates`, `GET /templates/search` working; JSON schema finalized |
| **5–6** | Phase 2 — Template Library (Frontend) | React template browser, filter chips, preview, one-click import | Template library UI complete; import flow to canvas working; 10+ starter templates created |
| **7–8** | Phase 3 — Tutorial System (Backend) | `Tutorial` model, `TutorialProgress` model, step validation logic | Tutorial CRUD API; progress tracking API; step validation service |
| **9–10** | Phase 4 — Tutorial System (Frontend) | Tutorial panel React component, step UI, progress bar, hint system | Interactive tutorial panel; 3 complete tutorials implemented (e.g., Half Adder, Full Adder, Counter) |
| **11** | Phase 5 — Contextual Help | Empty-state prompts, enhanced tooltips, first-time user tour | Onboarding overlay; contextual tooltips; help discovery layer |
| **12** | Polish & Documentation | Bug fixes, performance tuning, user testing, docs | All features stable; documentation written; final demo prepared |

---

## Deliverables

- [ ] **Template Library**: Searchable, filterable library with 20+ starter circuit templates
- [ ] **Template Import**: One-click import of any template into the editor canvas
- [ ] **Tutorial System**: In-editor guided tutorials with step-by-step instructions
- [ ] **Step Validation**: Real-time validation that guides users through each tutorial step
- [ ] **Progress Tracking**: Per-user tutorial progress saved to the database
- [ ] **Contextual Help**: Enhanced tooltips and empty-state onboarding prompts
- [ ] **First-Time User Tour**: Guided walkthrough for new accounts
- [ ] **Documentation**: User docs, contributor docs, and API documentation
- [ ] **Tests**: Jest tests for frontend components; RSpec tests for backend models/controllers

---

## Planned Contributions to CircuitVerse Codebase

Beyond the GSoC features, this project contributes back to the CircuitVerse ecosystem:

1. **New database models** (`Template`, `Tutorial`, `TutorialProgress`) — reusable patterns for future features
2. **Extended React component library** — template browser and tutorial panel components that can be adapted
3. **Enhanced tooltip system** — foundation for richer contextual help across the editor
4. **Circuit JSON serialization utilities** — improved handling of `.cv` format for template storage
5. **Tutorial authoring guide** — documentation enabling community members to create new tutorials
6. **Starter template collection** — a seed set of 20+ beginner-friendly circuits that serve as examples
7. **API documentation** — OpenAPI/Swagger specs for the new endpoints

---

## Repository Structure

| Folder | Purpose |
|--------|---------|
| `docs/` | Architecture diagrams, ERD, API specs, timeline, evaluation criteria |
| `research/templates/` | Template library design, metadata schemas, circuit examples |
| `research/tutorials/` | Tutorial flow concepts, step validation logic, challenge design |
| `research/ui-ux/` | Wireframes, user flows, tooltip concepts, onboarding flow diagrams |
| `templates/` | Sample circuit templates in `.cv` JSON format for testing |
| `tutorials/` | Sample tutorial definitions (JSON step sequences) |

---

## How to Use This Repository

This repository serves as my GSoC preparation workspace. The contents include:

- **Design documents** — architecture, data models, API specifications
- **Research notes** — analysis of CircuitVerse codebase, existing features, and gaps
- **Prototypes** — sample circuit templates and tutorial definitions
- **Timeline** — week-by-week implementation plan

All work is intended to be contributed upstream to the main CircuitVerse repository after review and approval from mentors.

---

## About Me

**Pavan C N** — First-year B.Tech student in Robotics Engineering at Garden City University, Bangalore. Passionate about digital systems, embedded programming, and open-source contribution. This GSoC project combines my interest in education technology with hands-on full-stack development.

**Skills**: C, C++, Python, JavaScript, React, Ruby on Rails basics, PostgreSQL

**GitHub**: [github.com/strangerwhoisharborofdoom](https://github.com/strangerwhoisharborofdoom)
