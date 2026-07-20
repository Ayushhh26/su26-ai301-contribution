# Contribution 2: Locust Performance Testing Harness

**Contribution Number:** 2
**Student:** Ayush Dodia
**Issue:** Mr-DooSun/fastapi-agent-blueprint#3
**Status:** In Progress (Phase III Complete / Draft PR Opened)

## Why I Chose This Issue
I chose issue #3 because performance and load testing are critical skills for backend engineering. The issue required building a reusable, headless Locust testing harness to validate concurrent traffic on a FastAPI application. It presented a great opportunity to learn about system architecture, API authentication constraints, and creating tools that prioritize a seamless Developer Experience (DX) without adding unnecessary CI overhead. 

## Understanding the Issue

### Problem Description
The repository needed a local performance testing harness to ensure the API can handle concurrent read and write flows. The maintainer explicitly requested a headless `make perf-test` target utilizing Locust that hits `/health` endpoints and authenticated `/v1/user` CRUD routes, while keeping the tool local-only and completely out of the CI pipeline.

### Expected Behavior
A `tests/perf/locustfile.py` script containing:
1. An always-on concurrent read flow (hitting `/health` and `/health/db`).
2. An authenticated CRUD flow against `/v1/user` that properly handles JWTs and cleans up its own database records.
3. A `make perf-test` command to execute the tests headlessly with configurable defaults.

### Current Behavior
Not applicable (new infrastructure addition). 

### Affected Components
This is a new tooling addition affecting local development operations. It requires creating `tests/perf/locustfile.py`, updating the root `Makefile`, and adding operational documentation to `docs/operations/performance-locust.md`.

## Reproduction Process

### Environment Setup
I pulled the latest `main` branch, created my `feat/locust-perf-testing` branch, and ran `make setup` followed by `make quickstart` to initialize the local SQLite database and spin up the API on port 8001.

### Steps to Reproduce
1. Attempted to hit the `/v1/user` endpoints using the default `admin/admin` credentials provided by the quickstart environment.
2. Traced the API routing and dependency injection layers to understand how the JWT authentication was protecting the routes.

### Reproduction Evidence
**Findings:** By analyzing the backend architecture, I discovered that the default quickstart `admin` account is hard-coded with an `is_bootstrap_admin = True` flag. Attempting to use these credentials for the Locust `/v1/user` tests instantly crashes the API with a `403 Forbidden` error. I realized a real admin account requires a manual 3-step browser provisioning flow, which heavily informed my solution design.

## Solution Approach

### Analysis
To prevent the harness from crashing for new developers who haven't manually configured an admin account, the Locust tests needed to be modular. I decided to split the load generation into an "always-on" Customer flow that works out-of-the-box, and an "env-gated" Admin flow that only executes if valid credentials are provided.

### Proposed Solution
1. Use Locust's `HttpUser` class to create a `CustomerAuthUser` and a `HealthCheckUser` that run automatically.
2. Create an `AdminCrudUser` that reads from `LOCUST_ADMIN_USERNAME` and `PASSWORD` environment variables, setting the Locust `abstract = True` class property if they are missing so the test safely bypasses it.
3. Add the `make perf-test` headless execution command and document the full baseline metrics.

### Implementation Plan
- **Understand:** Build a headless Locust harness that successfully navigates the dual-realm (Customer vs Admin) JWT authentication system without breaking the default developer experience.
- **Match:** Use Locust's `on_start` hook for authentication and `abstract = True` for safe credential gating.
- **Plan:**
  1. Create `tests/perf/locustfile.py` with the split user classes.
  2. Use `name="/v1/user/[id]"` in Locust requests to prevent metric explosion.
  3. Add the `perf-test` target to the `Makefile`.
  4. Write `docs/operations/performance-locust.md` with setup instructions and the baseline capture.
- **Implement:** Write the code and execute `make check` to ensure Ruff formatting passes.
- **Evaluate:** Run `make quickstart` and `make perf-test` to verify terminal output and zero-failure execution.

## Testing Strategy

### Unit / Automated Tests
- Verified `tests/perf/locustfile.py` is safely ignored by `pytest` (due to naming conventions) and `mypy` (due to pre-commit config exclusions).
- Formatted using `uv run ruff format` to ensure perfect CI style compliance.

### Manual Testing
- **Zero-Setup E2E:** Ran `make quickstart` followed by `make perf-test` with an empty `.env`. Verified the Customer and Health scenarios produced stats while the Admin scenario cleanly skipped itself.
- **Negative Auth:** Supplied invalid admin credentials and confirmed the login failure appeared loudly in Locust's failure table (401 error) without crashing the other concurrent users.
- **Baseline Capture:** Recorded an official local baseline of 341 total requests, 0 failures, 11.5 RPS, and a median latency of 3ms.

## Implementation Notes

### Week 3 Progress
**What I built:**
- Created `tests/perf/locustfile.py` implementing three scenarios: a zero-setup Customer Auth flow, a Health endpoint flow, and an environment-gated Admin CRUD flow.
- Added a `perf-test` headless target to the `Makefile` with overridable default variables (`PERF_USERS`, `PERF_SPAWN_RATE`, `PERF_RUN_TIME`).
- Drafted the operational documentation in `docs/operations/performance-locust.md` detailing the setup and output reading.

**Challenges faced:**
- **The Admin Auth Trap:** Realizing that the default `admin` account is hard-blocked from token generation meant I had to design the `abstract = True` fallback mechanism to protect the local developer experience.
- **Governance Drift:** I actively chose *not* to update the repository's `.claude/rules/commands.md` file with the new Makefile target, as touching that file triggers an internal Tier B governance review. This kept the PR strictly scoped to the maintainer's original request.

## Code Changes

**Files modified:**
- Added `tests/perf/locustfile.py`
- Modified `Makefile`
- Added `docs/operations/performance-locust.md`
- Modified `docs/README.md` and `docs/reference.md` (roadmap updates)

**Key commits:**
- `feat: Implement Locust performance testing harness (#3)`

**Approach decisions:**
- Grouped dynamic endpoints using the Locust `name` parameter so the terminal output remains clean and readable, instead of creating a new row for every generated UUID.

## Pull Request

**Branch Link:** https://github.com/Ayushhh26/fastapi-agent-blueprint/tree/feat/locust-perf-testing
**PR Link:** https://github.com/Mr-DooSun/fastapi-agent-blueprint/pull/293
**PR Description:** Implements a headless Locust performance testing harness featuring zero-setup Customer flows and properly env-gated Admin CRUD flows to protect the developer experience. Includes a documented local baseline of 11.5 RPS.

**Status:** Draft PR Opened (Awaiting self-review and maintainer feedback).

---

# Contribution 1: examples/url-shortener: CRUD + Taskiq cleanup worker

**Contribution Number:** 1
**Student:** Ayush Dodia
**Issue:** Mr-DooSun/fastapi-agent-blueprint#94
**Status:** Merged (Phase IV Complete)

## Why I Chose This Issue
I chose issue #94 to build a URL shortener domain featuring standard CRUD endpoints and a Taskiq background worker that automatically sweeps and deletes expired links. The problem requires mapping out a new domain using FastAPI and integrating async background jobs without the scope creep of analytics or routing redirects.

This issue aligns perfectly with my backend experience in Python and FastAPI. It offers a clearly bounded scope and provides an excellent opportunity to learn and navigate a professional, DDD-layered open-source architecture by studying and replicating the repository's existing worker patterns. I have left a comment on the issue stating my intent to work on it, and I am preparing to set up the local environment.

## Understanding the Issue

### Problem Description
The project requires a new educational example domain for a url-shortener. It needs standard CRUD operations to manage short links, plus a background worker to automatically sweep and delete expired links. Since this is a feature addition, nothing is currently "broken," but the repository lacks this specific pattern demonstration for future contributors.

### Expected Behavior
A fully functional `LinkService` exposed via two interfaces:
1. A FastAPI router (`POST /link`, `GET /link/{short_code}`, `DELETE /link/{short_code}`).
2. A Taskiq background worker (`cleanup_expired_links_task.py`) that periodically calls the service to remove expired database rows.

### Current Behavior
Not applicable (new feature addition).

### Affected Components
This will be a new, isolated domain. It requires creating a new `src/url_shortener/` directory mimicking the exact 10-file Domain-Driven Design (DDD) structure found in `examples/todo/` and `src/user/interface/worker/`.

## Reproduction Process

### Environment Setup
I cloned my fork locally and used the project's zero-config sandbox. I ran `make setup` which uses `uv` for dependency management. I encountered an interactive terminal prompt asking to replace the existing `.venv` environment, which I resolved by inputting yes. Following that, I ran `make quickstart` which successfully initialized the local SQLite database (`quickstart.db`) and started the FastAPI server with an in-memory task broker on port 8001.

### Steps to Reproduce
1. Run `make setup` in the project root to install dependencies.
2. Run `make quickstart` to spin up the local server and SQLite database.
3. Open a web browser and navigate to `http://127.0.0.1:8001/docs`.
4. Verify that the FastAPI Interactive Swagger UI loads and displays active domain routes.

### Reproduction Evidence
**Branch Link:** https://github.com/Ayushhh26/fastapi-agent-blueprint/tree/feat/url-shortener-worker
**Findings:** The local development sandbox initializes cleanly without Docker or external cloud keys. The API documentation is fully accessible, confirming the base routing and DDD architecture are operational. I am unblocked to implement the `url_shortener` domain.

## Solution Approach

### Analysis
To match the repository's strict architecture, the core business logic must be completely isolated in a `LinkService` (Domain layer). Both the HTTP Interface (FastAPI Router) and the Background Interface (Taskiq worker) will consume this exact same service, ensuring we write the logic once and expose it twice.

### Proposed Solution
I will scaffold `src/url_shortener/` using the 10-file structure from `examples/todo/`. I will define the `LinkDTO` schema and SQLAlchemy repository. Then, I will write the FastAPI endpoints and implement the `delete_expired()` method. Finally, I will wire up `cleanup_expired_links_task.py` to trigger that method, copying the worker pattern from `src/user/interface/worker/`.

### Implementation Plan
- **Understand:** Build a url-shortener CRUD API and an async worker to prune expired links without adding scope creep.
- **Match:** The architectural pattern exactly matches `examples/todo/` and `src/user/interface/worker/`.
- **Plan:**
  1. Copy the 10-file template into `src/url_shortener/`.
  2. Define `LinkDTO` and the `LinkModel` for the database.
  3. Implement `LinkService` with create, get, delete, and `delete_expired` methods.
  4. Wire up the FastAPI router for the HTTP endpoints.
  5. Create `cleanup_expired_links_task.py` to trigger the cleanup via Taskiq.
  6. Write tests in `tests/test_cleanup_task.py`.
- **Review:** Ensure compliance with the repository's rules (ADR 004): confirm model objects do not leave the repository.
- **Evaluate:** Run `make quickstart`, use `curl` to create an expired link, manually trigger the cleanup task, and verify row deletion. 

## Testing Strategy

### Unit Tests
- `[x]` **Test case 1:** Ensure `delete_expired()` accurately identifies and drops expired rows using an in-memory SQLite repository.
- `[x]` **Test case 2:** Ensure permanent links (without an expiration date) remain untouched during the bulk delete operation.
- `[x]` **Test case 3:** Ensure the FastAPI router endpoints enforce path validation (min 1, max 64 characters) on the `short_code` parameter.

### Manual Testing
- Ran `make quickstart` and manually exercised the API using the `curl` commands documented in my newly added `README.md`.
- Executed the background task inline to verify it successfully deleted the targeted database rows without throwing serialization errors.
- Ran the full `make check` suite locally to ensure all Ruff linting, Pytest suites, and dependency lockfiles (`uv.lock`) were perfectly synced.

## Implementation Notes

### Week 3 Progress
**What I built:**
- Scaffolded the `examples/url_shortener` domain mimicking the production DDD architecture.
- Built the `LinkService`, repository layer, and the Dependency Injection container (`UrlShortenerContainer`).
- Added the `POST`, `GET`, and `DELETE` HTTP endpoints.
- Implemented the Taskiq worker task to handle idempotent bulk deletions.

**Challenges faced:**
- **Naming Conventions:** Initially named the directory `url-shortener` (hyphenated), which forced me to use complex `sys.modules` package-manufacturing blocks in the test files to get the imports working. After maintainer feedback, I renamed it to `url_shortener` (underscored), which allowed me to scrap the test hacks and use clean, direct imports.
- **Documentation Bug:** Discovered that the project's own `InMemory` broker documentation was broken—running a standalone worker crash-looped with `RuntimeError: Inmemory brokers cannot listen`. 
- **DI Wiring:** When attempting to run the worker inline to bypass the crash loop, I encountered `AttributeError: 'Provide' object has no attribute` because bare scripts don't automatically wire the Dependency Injection container. I worked with the maintainer to resolve the walkthrough instructions.
- **Git Conflicts:** Successfully resolved a merge conflict in `examples/README.md` after another PR landed on `main` before mine.

## Code Changes

**Files modified:**
- Added `examples/url_shortener/` module (Domain, Infrastructure, Interface layers).
- Added `tests/unit/url_shortener/test_cleanup_task.py`.
- Updated `examples/README.md` catalog.
- Updated `uv.lock`.

**Key commits:**
- Added url shortener domain and background task execution ([Link to branch](https://github.com/Ayushhh26/fastapi-agent-blueprint/tree/feat/url-shortener-worker))

**Approach decisions:**
- Kept the inputs strictly as naive UTC per SQLite limitations, and documented this behavior in the README rather than over-engineering tz-aware validation.
- Chose not to include HTTP redirects or click analytics to strictly prevent scope creep, adhering closely to issue #94's requirements.

## Pull Request

**PR Link:** https://github.com/Mr-DooSun/fastapi-agent-blueprint/pull/239
**PR Description:** Adds a CRUD link domain plus a Taskiq cleanup task that shares the same `LinkService` as the HTTP router. Mirrors the canonical `examples/todo/` layout and the production worker pattern.

**Maintainer Feedback:**
- **June 18, 2026:** Suggested renaming the folder from a hyphen to an underscore to fix the import hacks, bounding the `short_code` parameter, and noted that my work uncovered a bug in their InMemory broker documentation.
- **June 20, 2026:** I applied the rename, added the `Annotated[str, Path(min_length=1, max_length=64)]` bounds, updated the walkthrough documentation, and resolved the merge conflict with `main`.

**Status:** Merged!

## Learnings & Reflections

### Technical Skills Gained
- Deepened my understanding of Dependency Injection (DI) in Python. I now practically understand the difference between a wired container and an unwired `@inject` marker.
- Gained hands-on experience with `Taskiq` for async background workers in a FastAPI environment.
- Improved my Git operations, specifically fetching, rebasing, and resolving merge conflicts using command-line diff tools.

### Challenges Overcome
PR fatigue. Dealing with multiple rounds of review, refactoring test suites due to folder naming, and untangling project-side documentation bugs required persistence. I learned how to communicate professionally with maintainers and when to gracefully hand off a minor documentation tweak to get the PR merged.

### What I'd Do Differently Next Time
I would verify module naming conventions against standard Python import rules earlier in the process. Realizing that a simple hyphen was causing all my test import headaches was a great lesson in adhering to language-level standards from day one.

### Resources Used
- [FastAPI Path Parameters Documentation](https://fastapi.tiangolo.com/tutorial/path-params/)
- Project's internal architecture guidelines and `examples/todo/` reference implementation.
