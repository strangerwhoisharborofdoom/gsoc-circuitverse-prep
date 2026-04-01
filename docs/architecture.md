# Architecture Notes

## Project Direction

This project aims to improve the beginner learning experience in CircuitVerse through two connected features:

1. **A circuit template library** that makes common digital logic circuits easy to discover and reuse.
2. **A guided learning workflow** that walks beginners through circuits step by step.

These features are meant to reduce the initial learning barrier and make the editor easier to explore from day one.

## Existing Stack

CircuitVerse uses:

- **Frontend:** React with JavaScript
- **Backend:** Ruby on Rails
- **Database:** PostgreSQL

This proposal intentionally stays close to the existing architecture rather than introducing unnecessary complexity.

## Template Library Design

### Data Structure

Each template would be associated with structured metadata stored in the database:

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Display name of the template |
| `description` | text | Short explanation of what the circuit does |
| `circuit_data` | JSON | The actual circuit definition |
| `tags` | array | Searchable tags (e.g., `adder`, `flip-flop`) |
| `difficulty` | enum | `beginner`, `intermediate`, `advanced` |
| `category` | string | Topic area (e.g., `combinational`, `sequential`) |
| `author_id` | integer | User who created/submitted the template |

### Backend Endpoints

- `GET /api/templates` — List all templates with optional filters
- `GET /api/templates/:id` — Get a single template
- `POST /api/templates` — Create a new template
- `PUT /api/templates/:id` — Update a template
- `DELETE /api/templates/:id` — Delete a template
- `GET /api/templates/search?q=...&tag=...&difficulty=...` — Search and filter

### Frontend Components

- `TemplateList` — Grid/list view of available templates
- `TemplateCard` — Individual template preview with metadata
- `TemplateFilter` — Search bar, tag filters, difficulty selector
- `TemplateImportModal` — Flow to import a template into the editor

## Learning Workflow Design

### Tutorial Structure

A tutorial consists of ordered steps that guide the user through building or understanding a circuit:

| Field | Type | Description |
|-------|------|-------------|
| `tutorial_id` | integer | Unique identifier |
| `step_number` | integer | Order of the step |
| `title` | string | Short step title |
| `instruction` | text | What the user should do |
| `target_state` | JSON | Expected circuit state after the step |
| `hint` | text | Optional hint if user gets stuck |
| `is_complete` | boolean | Whether the step has been completed |

### Progress Tracking

Progress would be tracked per user and per tutorial:

- Track which steps have been completed
- Store timestamps for each step
- Allow resuming a tutorial from where the user left off
- Show overall completion percentage

### Backend Endpoints

- `GET /api/tutorials` — List all tutorials
- `GET /api/tutorials/:id` — Get tutorial with steps
- `GET /api/tutorials/:id/progress` — Get user progress
- `PUT /api/tutorials/:id/progress` — Update progress

### Frontend Components

- `TutorialSidebar` — Panel showing current step, instructions, and progress
- `TutorialStep` — Individual step content with interactive hints
- `TutorialProgress` — Visual indicator of completion
- `TutorialHint` — Contextual tooltip or inline hint

## Contextual Help & Onboarding

### Empty State Guidance

When a user opens the editor for the first time:

- Show a brief welcome message
- Suggest starting with a template or a guided tutorial
- Provide a link to the template library

### Tooltips and Info Boxes

- Hover tooltips on editor toolbar buttons
- Contextual info boxes explaining circuit elements
- Inline hints during tutorial steps
- A dismissible onboarding tour for first-time users

## Design Principles

1. **Stay native to CircuitVerse** — The implementation should feel like a natural extension of the existing product.
2. **Progressive disclosure** — Show advanced options only when relevant; keep the default experience simple.
3. **No blocking UX** — Help and guidance should be available on demand, not forced on users.
4. **Maintainable code** — Follow CircuitVerse's existing patterns and conventions.
