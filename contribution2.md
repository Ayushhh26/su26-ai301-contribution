# su26-ai301-contribution

# Contribution 2: Add Locust performance testing

**Contribution Number:** 2  
**Student:** Ayush Dodia  
**Issue:** https://github.com/Mr-DooSun/fastapi-agent-blueprint/issues/3  
**Status:** Phase I Complete

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

[To be completed in Phase II]

### Steps to Reproduce

1. [Step 1]
2. [Step 2]
3. [Observed result]

### Reproduction Evidence

- **Commit showing reproduction:** [Link to commit in your fork]
- **Screenshots/logs:** [If applicable]
- **My findings:** [What you discovered during reproduction]

---

## Solution Approach

### Analysis

[Your analysis of the root cause - what's causing the issue?]

### Proposed Solution

[High-level description of your fix approach]

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** [Restate the problem]

**Match:** [What similar patterns/solutions exist in the codebase?]

**Plan:** [Step-by-step implementation plan]
1. [Modify file X to do Y]
2. [Add function Z]
3. [Update tests]

**Implement:** [Link to your branch/commits as you work]

**Review:** [Self-review checklist - does it follow the project's contribution guidelines?]

**Evaluate:** [How will you verify it works?]

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
