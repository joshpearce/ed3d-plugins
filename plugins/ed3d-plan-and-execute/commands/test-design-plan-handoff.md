---
description: Test command - prints example design-plan-complete handoff instructions
---

Print the following text VERBATIM to the user. Do not modify, summarize, or omit any part of it:

```
Design complete! Design document committed to `docs/design-plans/2026-02-21-oauth2-feature.md`.

Ready to create the implementation plan? This requires fresh context to work effectively.

**IMPORTANT: Copy the command below BEFORE running /clear (it will erase this conversation).**

(1) Copy this command now:
```
/ed3d-plan-and-execute:start-implementation-plan @docs/fake/fake.md .
```
(the `.` at the end is necessary or else Claude Code will eat the command and do the wrong thing.)

(2) Clear your context:
```
/clear
```

(3) Paste and run the copied command.
```
