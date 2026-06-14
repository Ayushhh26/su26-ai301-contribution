# su26-ai301-contribution

# Contribution 1: examples/url-shortener: CRUD + Taskiq cleanup worker

**Contribution Number:** 1  
**Student:** Ayush Dodia  
**Issue:** https://github.com/Mr-DooSun/fastapi-agent-blueprint/issues/94  
**Status:** Phase II Complete

---

## Why I Chose This Issue

I chose issue #94 to build a URL shortener domain featuring standard CRUD endpoints and a Taskiq background worker that automatically sweeps and deletes expired links. The problem requires mapping out a new domain using FastAPI and integrating async background jobs without the scope creep of analytics or routing redirects.

This issue aligns perfectly with my backend experience in Python and FastAPI. It offers a clearly bounded scope and provides an excellent opportunity to learn and navigate a professional, DDD-layered open-source architecture by studying and replicating the repository's existing worker patterns. I have left a comment on the issue stating my intent to work on it, and I am preparing to set up the local environment.

---

## Understanding the Issue

### Problem Description
The project requires a new educational example domain for a `url-shortener`. It needs standard CRUD operations to manage short links, plus a background worker to automatically sweep and delete expired links. Since this is a feature addition, nothing is currently "broken," but the repository lacks this specific pattern demonstration for future contributors.

### Expected Behavior
A fully functional `LinkService` exposed via two interfaces:
1. A FastAPI router (`POST /link`, `GET /link/{short_code}`, `DELETE /link/{short_code}`).
2. A Taskiq background worker (`cleanup_expired_links_task.py`) that periodically calls the service to remove expired database rows.

### Current Behavior
Not applicable (new feature addition).

### Affected Components
This will be a new, isolated domain. It requires creating a new `src/url_shortener/` directory mimicking the exact 10-file Domain-Driven Design (DDD) structure found in `examples/todo/` and `src/user/interface/worker/`.

---

## Reproduction Process

### Environment Setup
I cloned my fork locally and used the project's zero-config sandbox. I ran `make setup` which uses `uv` for dependency management. I encountered an interactive terminal prompt asking to replace the existing `.venv` environment, which I resolved by inputting `yes`. Following that, I ran `make quickstart` which successfully initialized the local SQLite database (`quickstart.db`) and started the FastAPI server with an in-memory task broker on port 8001.

### Steps to Reproduce
*(Note: As this is a feature addition, these steps verify the baseline architecture and sandbox environment run successfully before scaffolding the new domain).*
1. Run `make setup` in the project root to install dependencies.
2. Run `make quickstart` to spin up the local server and SQLite database.
3. Open a web browser and navigate to `http://127.0.0.1:8001/docs`.
4. Verify that the FastAPI Interactive Swagger UI loads and displays active domain routes (such as `/v1/todo` and `/v1/user`).

### Reproduction Evidence

- **Branch Link:** https://github.com/Ayushhh26/fastapi-agent-blueprint/tree/feat/url-shortener-worker
- **My findings:** The local development sandbox initializes cleanly without Docker or external cloud keys. The API documentation is fully accessible, confirming the base routing and DDD architecture are operational. I am unblocked to implement the `url_shortener` domain.

---

## Solution Approach

### Analysis
To match the repository's strict architecture, the core business logic must be completely isolated in a `LinkService` (Domain layer). Both the HTTP Interface (FastAPI Router) and the Background Interface (Taskiq worker) will consume this exact same service, ensuring we write the logic once and expose it twice.

### Proposed Solution
I will scaffold `src/url_shortener/` using the 10-file structure from `examples/todo/`. I will define the `LinkDTO` schema and SQLAlchemy repository. Then, I will write the FastAPI endpoints and implement the `delete_expired()` method. Finally, I will wire up `cleanup_expired_links_task.py` to trigger that method, copying the worker pattern from `src/user/interface/worker/`.

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** Build a `url-shortener` CRUD API and an async worker to prune expired links without adding scope creep like analytics or redirects.

**Match:** The architectural pattern exactly matches `examples/todo/` (for CRUD structure) and `src/user/interface/worker/` (for Taskiq integration).

**Plan:**
1. Copy the 10-file template from `examples/todo/` into `src/url_shortener/`.
2. Define `LinkDTO` (id, short_code, target_url, expires_at, created_at, updated_at) and the `LinkModel` for the database.
3. Implement `LinkService` with `create`, `get`, `delete`, and `delete_expired` methods.
4. Wire up the FastAPI router for the HTTP endpoints.
5. Create `cleanup_expired_links_task.py` to trigger the cleanup via Taskiq.
6. Write tests in `tests/test_cleanup_task.py` verifying expired rows drop and valid rows remain.

**Implement:** [Link to working branch will be added here during Phase III]

**Review:** Ensure compliance with the repository's rules (ADR 004): confirm model objects do not leave the repository and verify no domain-to-infrastructure imports exist before committing.

**Evaluate:** I will run `make quickstart`, use `curl` or Swagger UI to create an expired link, manually trigger the cleanup task, and verify the row is deleted from the database. I will also run `pytest tests/` to confirm all existing and new tests pass.

---

## Testing Strategy

### Unit Tests

- [ ] Test case 1: [Description]
- [ ] Test case 2: [Description]
- [ ] Test case 3: [Description]

### Integration Tests

- [ ] Integration scenario 1
- [ ] Integration scenario 2

### Manual Testing

[What you tested manually and results]

---

## Implementation Notes

### Week [X] Progress

[What you built this week, challenges faced, decisions made]

### Week [Y] Progress

[Continue documenting as you work]

### Code Changes

- **Files modified:** [List]
- **Key commits:** [Links to important commits]
- **Approach decisions:** [Why you chose certain approaches]

---

## Pull Request

**PR Link:** [GitHub PR URL when submitted]

**PR Description:** [Draft or final PR description - much of the content above can be adapted]

**Maintainer Feedback:**
- [Date]: [Summary of feedback received]
- [Date]: [How you addressed it]

**Status:** [Awaiting review / Iterating / Approved / Merged]

---

## Learnings & Reflections

### Technical Skills Gained

[What you learned technically]

### Challenges Overcome

[What was hard and how you solved it]

### What I'd Do Differently Next Time

[Reflection on your process]

---

## Resources Used

- [Link to helpful documentation]
- [Tutorial or Stack Overflow post that helped]
- [GitHub issues or discussions that helped]
