---
description: Log a new team decision OR an update to an existing one. Routes to the subject DRI for ✅ before going into the brain.
---

The user wants to log a decision or an update to an existing entry:

$ARGUMENTS

Walk through these fields with the user, asking only for what's missing:

1. **Title** — short name for the decision (e.g. "Math Academy 3-attempt cap")
2. **Mode** — is this a NEW decision, or an UPDATE to an existing one?
3. **What was decided / changed** — the actual content
4. **Why** (rationale) — optional but helpful
5. **Subject DRI email** — REQUIRED. Whose area does this fall in?
   (Ask the user; if they don't know, run `/alpha-academic-remote:ask-alpha-academic who owns <area>` first.)
6. **Decided on** — date or meeting reference
7. **Source** — link to chat thread / doc / meeting notes (REQUIRED for the DRI to verify)

Then POST to `$ALPHA_API_URL/feedback` with:

```json
{
  "kind": "proposed-edit",
  "message": "[NEW DECISION] / [UPDATE] <title>\n\nDecision: <content>\nWhy: <rationale>\nDecided on: <date>\nSource: <link>",
  "source": "<entity-slug-being-updated-if-an-update>",
  "claude_said": "<title>",
  "reported_by": "<user-email-from-context>"
}
```

The brain will:
1. Look up the subject DRI from `source` slug (if provided)
2. Auto-email them an approval request (cc'd to Tripti)
3. After they ✅, the entry lands in the brain on Monday's refresh

Confirm to the user that the approval email has been sent, and tell them
which DRI it went to.
