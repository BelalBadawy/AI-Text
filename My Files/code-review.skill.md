# BookLand ASP.NET Core Clean Architecture Code Review Skill

## Purpose
Use this skill to review the BookLand ASP.NET Core solution as an enterprise-grade Clean Architecture codebase, with strict attention to:
- .NET 10 / C# 14 compatibility
- DRY, SOLID, and maintainability
- Clean Architecture boundaries
- validation for every command
- API, application, infrastructure, and domain quality
- production readiness, security, and testability

## Target standards
- Target platform: .NET 10
- Language standard: C# 14
- Architecture style: Clean Architecture with thin API endpoints, application orchestration, domain purity, and infrastructure implementations
- Validation stack: FluentValidation
- Messaging pattern: request/handler style (Mediator/CQRS)
- API style: Minimal APIs or endpoint modules, but all endpoints must remain thin

## Review goals
1. Identify architecture drift and violations of Clean Architecture.
2. Find duplicated logic, copy/paste code, and unnecessary coupling.
3. Verify that every command has a validator and that validation is actually executed.
4. Detect code that is hard to test, hard to extend, or hard to change.
5. Flag anything that prevents the solution from being enterprise grade.
6. Recommend fixes that preserve the current style of the solution.

## Mandatory rules
### Validation
- Every command must implement `IValidateMe`.
- Every command must have a matching FluentValidation validator.
- Validators must live next to the command in the same feature folder.
- Validation must cover nested request objects, null safety, ranges, lengths, formats, and business invariants.
- Do not allow commands to bypass the validation pipeline.
- If a command has no validator, treat it as a high-severity issue.

### Clean Architecture boundaries
- Domain must not reference Infrastructure or API.
- Application must depend only on abstractions and domain types.
- Infrastructure must implement Application abstractions.
- API must only orchestrate requests and HTTP concerns.
- Business rules must not live in controllers/endpoints.

### DRY
- Remove repeated mapping, repeated guard clauses, repeated validation logic, repeated status code translation, and repeated permission checks.
- Prefer reusable helpers, base abstractions, and shared policy objects only when they truly reduce duplication.
- Do not create unnecessary abstraction layers.

### SOLID
- Single Responsibility: one class, one reason to change.
- Open/Closed: prefer extension over modification.
- Liskov: derived abstractions must remain substitutable.
- Interface Segregation: avoid fat interfaces.
- Dependency Inversion: depend on abstractions, not concrete services.

### C# 14 / .NET 10 review lens
- Prefer modern C# syntax when it improves clarity: file-scoped namespaces, target-typed `new`, pattern matching, primary constructors where appropriate, concise property initialization, and newer language features when they make code safer or simpler.
- Do not force new syntax if it makes the code harder to read.
- Confirm the project actually compiles with the intended language version.
- Flag unsupported or accidental preview usage if it leaks into stable code.

### ASP.NET Core quality checks
- Avoid sync-over-async.
- Avoid blocking startup code.
- Keep endpoint handlers thin.
- Use cancellation tokens in async paths.
- Centralize exception handling.
- Use proper authentication and authorization policies.
- Keep HTTP response translation in the API layer.

## Review checklist by layer
### Domain
- Entities should hold business invariants and core state only.
- No persistence, HTTP, caching, or identity concerns.
- Value objects, entities, and domain events should be intentional and consistent.
- Properties should not expose unsafe mutable state unless required.
- Naming should reflect the business domain clearly.

### Application
- Commands and queries should be small request contracts.
- Handlers should orchestrate, not contain infrastructure logic.
- Validators must exist for every command.
- Request/response models should be explicit and stable.
- Cross-cutting concerns should be handled through behaviors/pipeline steps.
- No direct dependency on concrete infrastructure services.

### Infrastructure
- Persistence should be isolated behind interfaces.
- Identity, email, file storage, caching, and background services should be swappable.
- Avoid hard-coded configuration values.
- Use `DateTime.UtcNow` through abstractions where possible.
- Keep EF Core configuration explicit and consistent.

### API
- Endpoints should do routing, binding, auth, and result translation only.
- Do not place business logic in endpoint lambdas.
- Keep route names, tags, and status codes consistent.
- Return standardized response wrappers.
- Protect endpoints with the correct authorization policy.

## Enterprise-grade quality scoring
Score each area from 0 to 5.

- Architecture boundaries
- Validation coverage
- Naming consistency
- SOLID adherence
- DRY adherence
- Async correctness
- Security and auth
- Testability
- Observability and error handling
- Maintainability

### Interpretation
- 45–50: enterprise grade
- 35–44: strong but needs polishing
- 25–34: workable but not enterprise grade yet
- below 25: needs major remediation

## Repo-specific checks for BookLand
When reviewing this repository, explicitly verify:
- Every command implements `IValidateMe`.
- Every command has a validator file in the same feature folder.
- Validation pipeline behavior is actually wired in and not bypassed.
- Endpoint namespaces are consistent across the solution.
- No stale references remain from other projects.
- Startup does not block on async work.
- No direct `DateTime.Now` usage leaks into business workflows.
- No empty marker interfaces are used without a clear purpose.
- No handler naming typos or inconsistent file names remain.
- No missing tests or absent test project structure remain.

## Current assessment guidance
For this solution, treat the current state as:
- Strong Clean Architecture foundation
- Good feature separation in many areas
- Validation infrastructure is present
- But not yet fully enterprise grade until command validation, naming consistency, startup async hygiene, solution-wide analyzers, and test coverage are tightened

## Findings format
When reporting issues, use this structure:
- Severity: Critical / High / Medium / Low
- File(s): exact path(s)
- Problem: what is wrong
- Why it matters: architectural or operational impact
- Fix: concrete remediation

## Output format
Return the review in this order:
1. Overall score
2. Executive summary
3. High-severity findings
4. Medium-severity findings
5. Low-severity findings
6. Missing validators and validation gaps
7. DRY/SOLID violations
8. C# 14 modernization opportunities
9. Enterprise-grade readiness verdict
10. Recommended next actions

## Review style
- Be direct.
- Be specific.
- Prefer evidence over opinion.
- Recommend changes that fit the existing style of the solution.
- Do not suggest rewriting the whole app unless the architecture is fundamentally broken.
