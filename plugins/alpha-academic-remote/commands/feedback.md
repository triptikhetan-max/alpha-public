---
description: Flag something wrong, missing, or out of date in the brain. If you're the DRI of that area, your edit goes live directly. Otherwise it auto-emails the subject DRI for ✅.
---

Use the ask-alpha-academic skill to POST feedback to the brain:

$ARGUMENTS

Walk through these fields with the user:

1. **What's wrong / missing** — their actual feedback (REQUIRED)
2. **Which entry?** — the entity slug or title being flagged (helps the
   brain look up the subject DRI). If they're not sure, ask "what subject
   /platform/policy is this about?" and infer.
3. **Are you the DRI of that area?** — ask only if not obvious from prior
   context. (The brain validates this server-side using the per-user API
   key, but knowing up-front lets us set the right `kind`.)
4. **Source** — where does the correct info come from? (chat thread,
   Drive doc, sheet, meeting). Strongly recommended.

POST to `$ALPHA_API_URL/feedback` with:

- If the user is the DRI of that area:
  ```json
  {
    "kind": "dri-direct-edit",
    "message": "<their feedback + correct version>",
    "source": "<entity-slug>",
    "claude_said": "<entity-title>",
    "reported_by": "<user-email>"
  }
  ```
  The brain treats this as auto-approved (caller is the DRI). It lands on
  Monday's refresh, no email round-trip.

- If the user is NOT the DRI:
  ```json
  {
    "kind": "proposed-edit",
    "message": "<their feedback + source link>",
    "source": "<entity-slug>",
    "claude_said": "<entity-title>",
    "reported_by": "<user-email>"
  }
  ```
  The brain looks up the subject DRI and auto-emails them an approval
  request (cc'd to Tripti). The response includes which DRI was notified.

- If the user just wants to flag a generic gap (no specific entity):
  ```json
  {
    "kind": "gap",
    "message": "<their feedback>",
    "reported_by": "<user-email>"
  }
  ```
  Goes to Tripti's weekly review log.

Tell the user what happened: "Sent to <DRI name>" or "Logged for review".
