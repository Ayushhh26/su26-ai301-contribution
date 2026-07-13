# su26-ai301-contribution

# Contribution 2: Add Locust performance testing

**Contribution Number:** 2  
**Student:** Ayush Dodia  
**Issue:** https://github.com/Mr-DooSun/fastapi-agent-blueprint/issues/3  
**Status:** Phase II Complete

---

## Why I Chose This Issue

After successfully building the `url_shortener` domain and Taskiq background worker for this exact repository, I wanted to transition into a DevOps and Site Reliability Engineering (SRE) focused challenge. Setting up Locust performance testing infrastructure is a perfect fit. Understanding how APIs behave under pressure, measuring throughput, and tracking response times are critical skills for production readiness. 

As I prepare for full-time software engineering roles, gaining hands-on experience with load testing frameworks directly complements the backend API work I've already completed. It also allows me to continue contributing to a familiar Domain-Driven Design (DDD) architecture while deepening my overall system design knowledge.

---

## Understanding the Issue

### Problem Description

The repository currently lacks performance testing infrastructure. There is no automated, baseline method to measure API response times, concurrency limits, or overall system stability under load before the application is deployed to production.

### Expected Behavior

The project needs Locust integrated as the official performance testing framework. Based on maintainer feedback, the solution must provide a "ready-to-adapt harness" for developers, including:
1. A `locustfile.py` containing pre-configured test scenarios (specifically an Auth + CRUD flow on `/v1/user` and a concurrent-request case).
2. A new `Makefile` target (`make perf-test`) to easily execute Locust headlessly against a local server.
3. Brief documentation explaining how to run the tests and read the terminal output.

### Current Behavior

No load testing infrastructure or configuration exists in the codebase. However, the `locust` package is already included in the project's `dev` dependency group, meaning no new package installation steps or lockfile updates are required.

### Affected Components

This is an infrastructure addition. It will primarily involve:
- Creating a new script at `tests/perf/locustfile.py` (or similar `perf/` directory).
- Modifying the `Makefile` to add the execution target.
- Updating the `README.md` or documentation folder to include execution instructions.

---

## Reproduction Process

### Environment Setup

A common challenge when setting up load-testing tools is resolving dependency conflicts with existing local servers. However, I resolved this proactively by utilizing my pre-existing `uv` environment from a previous contribution and verifying the maintainer's note that `locust` was safely isolated in the `dev` dependency group. I ran `make setup` to ensure all dependencies were fresh, avoiding any need for additional installation steps.

### Steps to Reproduce

Because this is a feature addition for missing infrastructure rather than a bug, reproduction involves verifying the absence of the tooling and ensuring the target server runs successfully:
1. Run `make quickstart` in the terminal to start the zero-infra local server and SQLite database.
2. Open a second terminal and search for a performance testing target by running `make perf-test`.
3. **Expected:** A Locust load-testing script executes against the local server.
4. **Actual:** The terminal returns `make: *** No rule to make target 'perf-test'.  Stop.`, confirming the infrastructure is currently missing.

### Reproduction Evidence

- **Working Branch:** https://github.com/Ayushhh26/fastapi-agent-blueprint/tree/feat/locust-perf-testing
- **My findings:** The local development sandbox initializes cleanly on `http://127.0.0.1:8001`. The `locust` CLI is accessible in the virtual environment (`locust -V` works), meaning I am unblocked to write the test script and `Makefile` target.

---

## Solution Approach

### Analysis

The repository requires a highly portable, zero-configuration performance testing harness. Because the repository is a "blueprint" for external adopters, the code needs to serve as an educational, ready-to-adapt harness rather than generating authoritative production numbers.

### Proposed Solution

I will build a headless Locust test suite targeting the `/v1/user` endpoints. I will define an `HttpUser` class that handles authentication and executes a mix of CRUD operations and concurrent requests. Finally, I will wire this script into the `Makefile` so it can be executed with a single command.

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** The codebase lacks a standardized way to measure API throughput and response times under load. It needs a lightweight Locust integration that targets the local development server.

**Match:** This mirrors standard SRE and DevOps load-testing patterns. I will utilize Locust's `HttpUser` class, taking advantage of the `on_start` method to handle API authentication flows before executing repeated `@task` methods.

**Plan:**
1. Create `tests/perf/locustfile.py`.
2. Write an `on_start` method to register and log in a simulated user, storing the JWT token in the client session headers.
3. Write tasks (`@task`) that perform CRUD operations (GET/POST) against `/v1/user`.
4. Update the `Makefile` with a new target: `perf-test: \n\t locust -f tests/perf/locustfile.py --headless -u 10 -r 2 -H http://127.0.0.1:8001`.
5. Update `README.md` (or the relevant docs section) with a short guide on how to run the command and interpret the terminal table.
6. **Edge Case Identification:** I will ensure the `on_start` authentication method caches the JWT token for the simulated user, rather than re-authenticating on every single request, which would artificially skew the performance metrics toward database read/write bottlenecks instead of standard API throughput.

**Implement:** https://github.com/Ayushhh26/fastapi-agent-blueprint/tree/feat/locust-perf-testing

**Review:** I will self-review against the project's formatting guidelines (running `make check` for Ruff linting) and ensure the script strictly targets the quickstart environment without introducing external dependencies.

**Evaluate:** I will execute `make quickstart` in one terminal and `make perf-test` in another. I will verify that Locust successfully authenticates, hits the endpoints with 200 OK responses, and outputs a complete performance metrics table in the terminal.

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
