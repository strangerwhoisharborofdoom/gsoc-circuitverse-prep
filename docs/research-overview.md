# Research Overview

This document summarizes the key research findings and design decisions that shaped the proposed GSoC 2026 project for CircuitVerse.

## The Problem We Are Solving

CircuitVerse is a powerful digital logic simulator, but new users face a steep learning curve when they first open the editor. A blank canvas gives freedom but also creates uncertainty. There is no guided path from "I know nothing" to "I can build a working circuit."

### Key Pain Points Identified

1. **No entry point** — The editor opens empty. Users do not know where to start.
2. **No examples to learn from** — CircuitVerse has few built-in example circuits.
3. **No progressive difficulty** — There is no structured path from simple to complex.
4. **Discoverability issues** — Features and components are not easy to find for beginners.

## Proposed Solution

The project focuses on three interconnected improvements:

### 1. Circuit Template Library

Pre-built, reusable circuit templates that users can:
- Load directly into the editor
- Modify and experiment with
- Save as starting points for their own designs

**Template categories**:
- Basic gates (AND, OR, NOT, XOR)
- Combinational circuits (adders, multiplexers, decoders)
- Sequential circuits (flip-flops, counters, shift registers)
- Small systems (ALU, memory units, simple CPUs)

### 2. Guided Tutorials

Interactive, step-by-step learning paths inside the simulator:
- Start with a blank template
- Follow prompts to build a specific circuit
- Get hints and explanations at each step
- Complete mini-challenges to reinforce learning

**Tutorial progression**:
- Level 1: Basic gates and truth tables
- Level 2: Combining gates into circuits
- Level 3: Sequential logic and timing
- Level 4: Building complete systems

### 3. UI/UX Improvements

Better discoverability and onboarding:
- A welcome screen when opening the editor for the first time
- Quick-access templates panel
- Contextual help tooltips
- Improved component search and categorization

## Technical Approach

### Backend (Ruby on Rails)
- Template storage in PostgreSQL
- RESTful API endpoints for template CRUD
- Tutorial progress tracking per user

### Frontend (React)
- Template browser UI
- Interactive tutorial renderer
- Inline hints and explanations

### Design Principles
- Keep the editor clean and uncluttered
- Use progressive disclosure — show advanced options only when needed
- Provide feedback at every step
- Make every action reversible

## References

- [CircuitVerse GitHub](https://github.com/CircuitVerse/CircuitVerse)
- [CircuitVerse Documentation](https://docs.circuitverse.org)
- [GSoC 2026 Project Ideas](https://www.circuitverse.org/gsoc)

---

*This research was conducted as part of GSoC 2026 proposal preparation.*
