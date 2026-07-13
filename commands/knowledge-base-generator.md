---
description: Research and record a recurring error
argument-hint: [error-or-context]
---

Use the **knowledge-base-generator** skill to investigate the recurring failure described by `$ARGUMENTS`.

1. Read the repository instructions before acting.
2. Confirm the same underlying failure has recurred enough to trigger the skill; if the arguments are empty, derive the failure from the current conversation.
3. Capture the stable error fragment and the affected technology.
4. Search upstream GitHub issues and authoritative documentation for the root cause and safe workarounds.
5. Create or update the project `AGENTS.md` knowledge-base entry without duplicating an existing entry.
6. Apply a workaround only when it is within the user's requested scope and the repository's safety rules.
7. Re-run the failing check and report the source links, outcome, and remaining uncertainty.

Do not invent a recurring error or claim that a workaround works without fresh verification.
