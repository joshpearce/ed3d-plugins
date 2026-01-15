---
name: starting-a-design-plan
description: Use when beginning any design process - orchestrates gathering context, clarifying requirements, brainstorming solutions, and documenting validated designs to create implementation-ready design documents
---

# Starting a Design Plan

## Overview

Orchestrate the complete design workflow from initial idea to implementation-ready documentation through five structured phases: context gathering, clarification, brainstorming, design documentation, and planning handoff.

**Core principle:** Progressive information gathering -> clear understanding -> creative exploration -> validated design -> documented plan.

**Announce at start:** "I'm using the starting-a-design-plan skill to guide us through the design process."

## Quick Reference

| Phase | Key Activities | Output |
|-------|---------------|--------|
| **1. Context Gathering** | Ask for freeform description, constraints, goals, URLs, files | Initial context bundle |
| **2. Clarification** | Invoke asking-clarifying-questions skill | Disambiguated requirements |
| **3. Brainstorming** | Invoke brainstorming skill | Validated design (in conversation) |
| **4. Design Documentation** | Invoke writing-design-plans skill | Committed design document |
| **5. Planning Handoff** | Offer to invoke writing-plans skill | Implementation plan (optional) |

## The Process

**REQUIRED: Create TodoWrite tracker at start**

Use TodoWrite to create todos for each phase:

- Phase 1: Context Gathering (initial information collected)
- Phase 2: Clarification (requirements disambiguated)
- Phase 3: Brainstorming (design validated)
- Phase 4: Design Documentation (design written to docs/design-plans/)
- Phase 5: Planning Handoff (implementation plan offered/created)

Mark each phase as in_progress when working on it, completed when finished.

### Phase 1: Context Gathering

**Never skip this phase.** Even if the user provides detailed information, ask for anything missing.

Mark Phase 1 as in_progress in TodoWrite.

**Ask the user to provide (freeform, not AskUserQuestion):**

"I need some information to start the design process. Please provide what you have:

**What are you designing?**
- High-level description of what you want to build
- Goals or success criteria
- Any known constraints or requirements

**Context materials (very helpful if available):**
- URLs to relevant documentation, APIs, or examples
- File paths to existing code or specifications in this repository
- Any research you've already done

**Project state:**
- Are you starting fresh or extending existing functionality?
- Are there existing patterns in the codebase I should follow?
- Any architectural decisions already made?

Share whatever details you have. We'll clarify anything unclear in the next step."

**Progressive prompting:** If user already provided some of this information, acknowledge what you have and ask only for what's missing.

**Example:**
"You mentioned OAuth2 integration. I have the high-level goal. To help design this effectively, I need:
- Any constraints (regulatory, existing auth system, etc.)
- URLs to the OAuth2 provider's documentation (if you have them)
- Whether this is for human users, service accounts, or both"

Mark Phase 1 as completed when you have initial context.

### Phase 2: Clarification

Mark Phase 2 as in_progress in TodoWrite.

**REQUIRED SUB-SKILL:** Use ed3d-plan-and-execute:asking-clarifying-questions

Announce: "I'm using the asking-clarifying-questions skill to make sure I understand your requirements correctly."

The clarification skill will:
- Use subagents to try to disambiguate before raising questions to the user
- Disambiguate technical terms ("OAuth2" -> which flow?)
- Identify scope boundaries ("users" -> humans? services? both?)
- Clarify assumptions ("integrate with X" -> which version?)
- Understand constraints ("must use Y" -> why?)

**Output:** Clear understanding of what user means, ready for brainstorming.

Mark Phase 2 as completed when requirements are disambiguated.

### Phase 3: Brainstorming

With clear understanding from Phase 2, explore design alternatives and validate the approach.

Mark Phase 3 as in_progress in TodoWrite.

**REQUIRED SUB-SKILL:** Use ed3d-plan-and-execute:brainstorming

Announce: "I'm using the brainstorming skill to explore design alternatives and validate the approach."

**Pass context to brainstorming:**
- Information gathered in Phase 1
- Clarifications from Phase 2
- This reduces Phase 1 of brainstorming (Understanding) since much is already known

The brainstorming skill will:
- Complete any remaining understanding gaps (Phase 1)
- Propose 2-3 architectural approaches (Phase 2)
- Present design incrementally for validation (Phase 3)
- Use research agents for codebase patterns and external knowledge

**Output:** Validated design held in conversation context.

Mark Phase 3 as completed when design is validated.

### Phase 4: Design Documentation

Write the validated design to a permanent, structured document.

Mark Phase 4 as in_progress in TodoWrite.

**REQUIRED SUB-SKILL:** Use ed3d-plan-and-execute:writing-design-plans

Announce: "I'm using the writing-design-plans skill to document the validated design."

The writing-design-plans skill will:
- Write design to `docs/design-plans/YYYY-MM-DD-<topic>.md`
- Structure with implementation phases (<=8 recommended)
  - DO NOT pad out phases in order to reach the number of 8. 8 is the maximum, not the target.
- Document existing patterns followed
- Commit to git

**Output:** Committed design document ready for implementation planning.

Mark Phase 4 as completed when design document is committed.

### Phase 5: Planning Handoff

After design is documented, guide user to create implementation plan in fresh context.

Mark Phase 5 as in_progress in TodoWrite.

**Do NOT create implementation plan directly.** The user needs to compact context first.

Announce design completion and provide next steps:

```
Design complete! Design document committed to `docs/design-plans/[filename]`.

Ready to create the implementation plan? This requires fresh context to work effectively.

Run these commands in sequence:

(1) Compact your context first:
```
/compact
```

(2) Then create the implementation plan:
```
/ed3d-ed3d-plan-and-execute:start-implementation-plan @docs/design-plans/[full-filename].md .
```

(the `.` at the end is necessary or else Claude Code will eat the command and do the wrong thing.)

The start-implementation-plan command will create detailed tasks, set up a branch, and prepare for execution.
```

**Why two separate commands:**
- User can only paste one command at a time
- /compact must run first to free up context
- Implementation planning needs fresh context for codebase investigation

Mark Phase 5 as completed after providing instructions.

## When to Revisit Earlier Phases

You can and should go backward when:
- Phase 2 reveals fundamental gaps -> Return to Phase 1
- Phase 3 uncovers new constraints -> Return to Phase 1 or 2
- User questions approach during Phase 3 -> Return to Phase 2
- Design documentation reveals missing details -> Return to Phase 3

**Don't force forward linearly** when going backward gives better results.

## Common Rationalizations - STOP

| Excuse | Reality |
|--------|---------|
| "User provided details, can skip context gathering" | Always run Phase 1. Ask for what's missing. |
| "Requirements are clear, skip clarification" | Clarification prevents misunderstandings. Always run Phase 2. |
| "Simple idea, skip brainstorming" | Brainstorming explores alternatives. Always run Phase 3. |
| "Design is in conversation, don't need documentation" | Documentation is contract with writing-implementation-plans. Always run Phase 4. |
| "Can invoke implementation planning directly" | Must compact first. Provide two-command workflow. |
| "I can combine phases for efficiency" | Each phase has distinct purpose. Run all five. |
| "User knows what they want, less structure needed" | Structure ensures nothing is missed. Follow all phases. |

**All of these mean: STOP. Run all five phases in order.**

## Key Principles

| Principle | Application |
|-----------|-------------|
| **Never skip brainstorming** | Even with detailed specs, always run Phase 3 (may be shorter) |
| **Progressive prompting** | Ask for less if user already provided some context |
| **Clarify before ideating** | Phase 2 prevents building the wrong thing |
| **All brains in skills** | This skill orchestrates; sub-skills contain domain expertise |
| **TodoWrite tracking** | YOU MUST create and update todos for all five phases |
| **Flexible progression** | Go backward when needed to fill gaps |
