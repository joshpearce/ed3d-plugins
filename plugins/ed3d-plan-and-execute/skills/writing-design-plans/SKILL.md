---
name: writing-design-plans
description: Use after brainstorming completes - writes validated designs to docs/design-plans/ with structured format and discrete implementation phases required for creating detailed implementation plans
---

# Writing Design Plans

## Overview

Transform validated design from brainstorming conversation into permanent, structured documentation that serves as the contract for implementation planning.

**Core principle:** Convert conversation to documentation. Structure for implementation. Commit for permanence.

**Announce at start:** "I'm using the writing-design-plans skill to document the validated design."

**Context:** Design should already be validated through brainstorming. This skill documents it, not creates it.

## File Location and Naming

**Save to:** `docs/design-plans/YYYY-MM-DD-<topic>.md`

**Use actual date and descriptive topic:**
- Good: `docs/design-plans/2025-01-18-oauth2-service-auth.md`
- Good: `docs/design-plans/2025-01-18-user-profile-redesign.md`
- Bad: `docs/design-plans/design.md`
- Bad: `docs/design-plans/new-feature.md`

## Required Document Structure

**EVERY design plan MUST follow this structure:**

```markdown
# [Feature Name] Design

## Overview
[High-level description in 2-3 sentences]

[Goals and success criteria from brainstorming Phase 1]

## Architecture
[Approach selected in brainstorming Phase 2]

[Key components and how they interact]

[Data flow and system boundaries]

## Existing Patterns
[Document codebase patterns discovered by investigator that this design follows]

[If introducing new patterns, explain why and note divergence from existing code]

[If no existing patterns found, state that explicitly]

## Implementation Phases

Break implementation into discrete phases (<=8 recommended):

### Phase 1: [Name]
**Goal:** What this phase achieves

**Components:** What gets built/modified (exact paths from investigator)

**Dependencies:** What must exist first

**Done when:** How to verify this phase is complete (see Phase Verification below)

### Phase 2: [Name]
[Same structure]

...continue for each phase...

## Additional Considerations
[Error handling, edge cases, future extensibility - only if relevant]

[Don't include hypothetical "nice to have" features]
```

## Implementation Phases: Critical Requirements

**YOU MUST break design into discrete, sequential phases.**

**Each phase should:**
- Achieve one cohesive goal
- Build on previous phases (explicit dependencies)
- End with a working build and clear "done" criteria
- Use exact file paths and component names from codebase investigation

## Phase Verification

**Verification depends on what the phase delivers:**

| Phase Type | Done When | Examples |
|------------|-----------|----------|
| Infrastructure/scaffolding | Operational success | Project installs, builds, runs, deploys |
| Functionality/behavior | Tests pass for new behavior | Unit tests, integration tests, E2E tests |

**The rule:** If a phase adds code that implements behavior, that phase includes tests proving the behavior works. Tests are a deliverable of the phase, not a separate "testing phase" later.

**Don't over-engineer infrastructure verification.** You don't need unit tests for package.json. "npm install succeeds" is sufficient verification for a dependency setup phase.

**Do require tests for functionality.** Any code that does something needs tests that prove it does that thing. These tests are part of the phase, not deferred.

**Tests can evolve.** A test written in Phase 2 may be modified in Phase 4 as requirements expand. This is expected. The constraint is that Phase 2 ends with passing tests for what Phase 2 delivers.

**Structure phases as subcomponents.** A phase may contain multiple logical subcomponents. Each subcomponent should have its tests grouped with it, not deferred to the end of the phase.

Good structure (tests per subcomponent):
```
Phase 2: Core Services
- Task 1: TokenPayload type and TokenConfig
- Task 2: TokenService implementation
- Task 3: TokenService tests
- Task 4: UserSession type
- Task 5: SessionManager implementation
- Task 6: SessionManager tests
```

Bad structure (all tests at end):
```
Phase 2: Core Services
- Task 1: TokenPayload type and TokenConfig
- Task 2: TokenService implementation
- Task 3: UserSession type
- Task 4: SessionManager implementation
- Task 5: All tests for TokenService and SessionManager
```

This structure allows the execution agent to group Tasks 1-3 as one subcomponent and Tasks 4-6 as another, verifying each before moving on.

**Phase count:**
- Target: 5-8 phases (sweet spot for planning)
- Maximum: 8 phases (hard limit for writing-plans skill)
- If >8 phases needed: Note that multiple implementation plans will be required

**Why <=8 phases matters:**
- writing-plans skill has hard limit of 8 phases per implementation plan
- Exceeding 8 phases forces user to scope or split
- This is by design to prevent overwhelming implementation plans

**If design needs >8 phases:**

Add note to Additional Considerations:
```markdown
## Additional Considerations

**Implementation scoping:** This design has [N] phases total. The writing-plans skill limits implementation plans to 8 phases. Consider:
1. Implementing first 8 phases in initial plan
2. Creating second implementation plan for remaining phases
3. Simplifying design to fit within 8 phases
```

## Using Codebase Investigation Findings

**Include exact paths and structure from investigation:**

Good Phase definitions:

**Infrastructure phase example:**
```markdown
### Phase 1: Project Setup
**Goal:** Initialize project structure and dependencies

**Components:**
- Create: `package.json` with auth dependencies (jsonwebtoken, bcrypt)
- Create: `tsconfig.json` with strict mode
- Create: `src/index.ts` (minimal entry point)

**Dependencies:** None (first phase)

**Done when:** `npm install` succeeds, `npm run build` succeeds, no type errors
```

**Functionality phase example:**
```markdown
### Phase 2: Token Generation Service
**Goal:** Implement JWT token generation and validation

**Components:**
- Create: `src/services/auth/token-service.ts`
- Create: `src/services/auth/validator.ts`
- Create: `tests/services/auth/token-service.test.ts`
- Create: `tests/services/auth/validator.test.ts`

**Dependencies:** Phase 1 (project setup)

**Done when:**
- Unit tests pass for token generation (create, sign, verify)
- Validator tests pass (valid tokens accepted, invalid rejected, expired rejected)
- Build succeeds with no type errors
```

Bad Phase definition:
```markdown
### Phase 1: Authentication
**Goal:** Add auth stuff

**Components:** Auth files

**Dependencies:** Database maybe

**Testing:** Make sure it works
```

## Writing Style

**REQUIRED SUB-SKILL:** Use house-style:writing-for-a-technical-audience if available.

Otherwise follow these guidelines:

**Be concise:**
- Remove throat-clearing
- State facts directly
- Skip obvious explanations

**Be specific:**
- Use exact component names
- Reference actual file paths
- Include concrete examples

**Be honest:**
- Acknowledge unknowns
- State assumptions explicitly
- Don't over-promise

**Example - Good:**
```markdown
## Architecture

Service-to-service authentication using OAuth2 client credentials flow.

Auth service (`src/services/auth/`) generates and validates JWT tokens. API middleware (`src/api/middleware/auth.ts`) validates tokens on incoming requests. Token store (`src/data/token-store.ts`) maintains revocation list in PostgreSQL.

Tokens expire after 1 hour. Refresh not needed for service accounts (can request new token).
```

**Example - Bad:**
```markdown
## Architecture

In this exciting new architecture, we'll be implementing a robust and scalable authentication system that leverages the power of OAuth2! The system will be designed with best practices in mind, ensuring security and performance at every level. We'll use industry-standard JWT tokens that provide excellent flexibility and are widely supported across the ecosystem. This will integrate seamlessly with our existing infrastructure and provide a solid foundation for future enhancements!
```

## Existing Patterns Section

**Purpose:** Document what codebase investigation revealed.

**Include:**
- Patterns this design follows from existing code
- Why those patterns were chosen (if known)
- Any divergence from existing patterns with justification

**If following existing patterns:**
```markdown
## Existing Patterns

Investigation found existing authentication in `src/services/legacy-auth/`. This design follows the same service structure:
- Service classes in `src/services/<domain>/`
- Middleware in `src/api/middleware/`
- Data access in `src/data/`

Token storage follows pattern from `src/data/session-store.ts` (PostgreSQL with TTL).
```

**If no existing patterns:**
```markdown
## Existing Patterns

Investigation found no existing authentication implementation. This design introduces new patterns:
- Service layer for business logic (`src/services/`)
- Middleware for request interception (`src/api/middleware/`)

These patterns align with functional core, imperative shell separation.
```

**If diverging from existing patterns:**
```markdown
## Existing Patterns

Investigation found legacy authentication in `src/auth/`. This design diverges:
- OLD: Monolithic `src/auth/auth.js` (600 lines, mixed concerns)
- NEW: Separate services (`token-service.ts`, `validator.ts`) following FCIS

Divergence justified by: Legacy code violates FCIS pattern, difficult to test, high coupling.
```

## Additional Considerations

**Only include if genuinely relevant:**

**Error handling** - if not obvious:
```markdown
## Additional Considerations

**Error handling:** Token validation failures return 401 with generic message (don't leak token details). Service-to-service communication failures retry 3x with exponential backoff before returning 503.
```

**Edge cases** - if non-obvious:
```markdown
**Edge cases:** Clock skew handled by 5-minute token validation window. Revoked tokens remain in database for 7 days for audit trail.
```

**Future extensibility** - if architectural decision enables future features:
```markdown
**Future extensibility:** Token claims structure supports adding user metadata (currently unused). Enables future human user authentication without architecture change.
```

**Do NOT include:**
- "Nice to have" features not in current design
- Hypothetical future requirements
- Generic platitudes ("should be secure", "needs good testing")

## After Writing

**Commit the design document:**

```bash
git add docs/design-plans/YYYY-MM-DD-<topic>.md
git commit -m "$(cat <<'EOF'
docs: add [feature name] design plan

Completed brainstorming session. Design includes:
- [Key architectural decision 1]
- [Key architectural decision 2]
- [N] implementation phases
EOF
)"
```

**Announce completion:**

"Design plan documented in `docs/design-plans/YYYY-MM-DD-<topic>.md` and committed."

## Common Rationalizations - STOP

| Excuse | Reality |
|--------|---------|
| "Design is simple, don't need phases" | Phases make implementation manageable. Always include. |
| "Phases are obvious, don't need detail" | writing-plans needs exact paths. Provide them. |
| "Can have 10 phases if needed" | Hard limit is 8. Scope or split. |
| "Additional considerations should be comprehensive" | Only include if relevant. YAGNI applies. |
| "Should document all future possibilities" | Document current design only. No hypotheticals. |
| "Existing patterns section can be skipped" | Shows investigation happened. Always include. |
| "Can use generic file paths" | Exact paths from investigation. No handwaving. |
| "Tests can be a separate phase at the end" | No. Tests for functionality belong in the phase that creates that functionality. |
| "We'll add tests after the code works" | Phase isn't done until its tests pass. Tests are deliverables, not afterthoughts. |
| "Infrastructure needs unit tests too" | No. Infrastructure verified operationally. Don't over-engineer. |
| "Phase 3 tests will cover Phase 2 code" | Each phase tests its own deliverables. Later phases may extend tests, but don't defer. |

**All of these mean: STOP. Follow the structure exactly.**

## Integration with Workflow

This skill receives validated design from brainstorming:

```
Brainstorming Phase 3 completes
  -> Validated design exists in conversation
  -> User approved incrementally

Writing Design Plans (this skill)
  -> Extract design from conversation
  -> Structure with required sections
  -> Add exact paths from investigation
  -> Create discrete phases (<=8)
  -> Commit to git

Writing Plans (next step)
  -> Reads this design document
  -> Uses phases as basis for detailed tasks
  -> Expects exact paths and structure
```

**Purpose:** Create contract between design and implementation. Writing-plans relies on this structure.
