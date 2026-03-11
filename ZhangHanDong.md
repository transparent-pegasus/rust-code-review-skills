---
name: review-rust
description: Review Rust code against the Rust Code Review Guidelines (RCRG). Use when the user asks to review Rust code, check Rust code quality, or validate Rust code against best practices.
allowed-tools: Read, Grep, Glob, Bash, Agent
---
# Rust Code Review
Review the provided Rust code (file paths, diffs, or pasted snippets via `$ARGUMENTS`) against the **Rust Code Review Guidelines (RCRG)**.
If `$ARGUMENTS` contains file paths or glob patterns, read those files. If it contains a GitHub PR URL, fetch the diff. If empty, ask the user what code to review.
## Review Dimensions
Evaluate the code across **all** of the following dimensions. For each, assign a severity when reporting issues: **critical**, **warning**, or **suggestion**.
### 1. Correctness
- Code compiles without warnings (fix or document any warnings)
- Business logic is sound; no overlooked edge cases
- Error handling is appropriate (`Result`, `Option`, `?` operator, custom error types)
- Any `unsafe` code is correct, minimal, well-documented, and has safety comments explaining invariants
### 2. Readability
- Code is easy to read and understand at a glance
- Naming is clear, descriptive, and follows Rust conventions (`snake_case` for functions/variables, `CamelCase` for types, `SCREAMING_SNAKE_CASE` for constants)
- Comments explain *why*, not *what*; complex sections are documented
- Code is reasonably organized into functions and modules
### 3. Maintainability
- No duplicated logic; consolidation opportunities identified
- Generic, reusable abstractions where appropriate (but not over-abstracted)
- Tests cover key paths and edge cases
- Documentation comments (`///` and `//!`) explain implementation details
- Code structure follows Single Responsibility and Open/Closed principles
- Coupling between modules is examined and minimized
### 4. Safety & Security
- `unsafe` blocks have proper safety abstractions, especially at FFI boundaries
- No potential buffer overflows, SQL injections, or other vulnerabilities
- Input validation and sanitization are present where needed
- Concurrent code: check for deadlocks, improper synchronization, data races from incorrect memory ordering in lock-free or `unsafe` scenarios
- Authentication and authorization are properly implemented where relevant
- Dependencies are reliable, actively maintained, and not deprecated (supply chain security)
### 5. Performance
- No unnecessary memory allocations (excessive `clone()`, `to_string()`, `collect()` where iterators suffice)
- Data structures and algorithms are appropriate for the use case
- Opportunities for lazy evaluation, async processing, or parallelism are noted
- Performance benchmarks exist before optimization to prevent regression
- Compilation size, compilation time, and runtime performance are balanced for the scenario
### 6. API Design
- APIs are consistent, intuitive, and ergonomic
- Naming, interfaces, and documentation are clear
- API behavior is fully specified (no undocumented side effects)
- `unsafe` code is not unnecessarily exposed in public APIs
- Builder patterns, type-state patterns, or newtype patterns used where they improve safety
### 7. Debuggability
- Adequate logging at critical paths and potential error points (clear, meaningful log messages)
- Error messages are specific, with enough context to locate issues
- Assertions (`debug_assert!`, `assert!`) verify assumptions at key points
- Complex code sections have sufficient comments and documentation
### 8. Observability
- Integration with monitoring tools; adequate metrics for system health and performance
- Distributed tracing (e.g., OpenTelemetry) if applicable
- Appropriate alert thresholds and notification mechanisms
- Health check endpoints for services
### 9. Cross-cutting Concerns
- Cross-platform compatibility if the code runs on multiple platforms
- CI/CD compatibility (code compiles and tests pass in CI)
## Output Format
Structure the review as follows:
```
## Rust Code Review: <file or scope>
### Summary
<1-3 sentence overall assessment>
### Findings
#### Critical
- [Dimension] file:line — description and suggested fix
#### Warnings
- [Dimension] file:line — description and suggested fix
#### Suggestions
- [Dimension] file:line — description and suggested fix
### Score (optional, if user requests)
| Dimension        | Score (1-5) | Notes |
|------------------|-------------|-------|
| Correctness      |             |       |
| Readability      |             |       |
| Maintainability  |             |       |
| Safety           |             |       |
| Performance      |             |       |
| API Design       |             |       |
| Debuggability    |             |       |
| Observability    |             |       |
```
If no issues are found in a severity category, omit that category. Always provide at least one actionable suggestion, even for high-quality code.
