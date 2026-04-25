---
description: Flag a gap, correction, or new fact about Alpha academic workflows. Sends to the server-side feedback log for Tripti's weekly review.
---

Use the ask-alpha-academic skill to POST feedback to the Alpha Academic API:

$ARGUMENTS

Call `POST $ALPHA_API_URL/feedback` with a JSON body containing:
- `kind`: "correction" | "gap" | "new-fact" (default to "correction" if unclear)
- `message`: what the user typed
- `reported_by`: ask them their name if not obvious from context
- `claude_said`: quote Claude's previous wrong/missing answer if applicable
- `source`: where the correct info came from (chat thread, person, date) if the user mentioned it

Then confirm to the user that it's been logged for Tripti's review.
