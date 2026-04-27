---
description: Answer Alpha Schools academic questions (DRI lookups, policy questions, playbooks for "what do I do if X", document search) by calling the private Alpha Academic API. Use whenever someone references an Alpha subject (Math, Reading, Writing, Social Studies, Science, Language, Vocabulary, Fast Math), platform (Math Academy, Zearn, AlphaRead, AlphaWrite, Freckle, Lalilo, Membean, VocabLoco, DreamUp, Synthesis Tutor, etc.), policy (3-attempt cap, bracketing, hole filling, mastery threshold, custom plans, coaching), or asks "who owns X" / "where's Y doc" / "what do I do if Z".
---

# Ask the Alpha Academic API

This skill answers Alpha academic questions by calling a private HTTPS API. The knowledge base lives server-side — this plugin is a thin client, so it's safe to distribute publicly.

## Prerequisites

Two environment variables must be set before the skill will work:

- `ALPHA_API_URL` — the base URL of the Alpha Academic API (e.g. `https://alpha-academic.up.railway.app`). Tripti provides this.
- `ALPHA_API_KEY` — the personal API key issued to this teammate. Tripti provides this via secure channel (not in chat).

If either is unset, stop and tell the user:

> The Alpha Academic plugin needs two env vars set:
> `export ALPHA_API_URL="https://<your-api>.railway.app"`
> `export ALPHA_API_KEY="<your-key-from-tripti>"`
> Put both in your `~/.zshrc` or `~/.bashrc`, reload your shell, and try again.

## Workflow

### 1. Check env vars
Run:
```bash
test -n "$ALPHA_API_URL" && test -n "$ALPHA_API_KEY" && echo OK || echo MISSING
```
If `MISSING`, show the user the instructions above and stop.

### 2. For question / lookup / playbook requests → POST /ask
```bash
curl -sS -X POST "$ALPHA_API_URL/ask" \
  -H "Content-Type: application/json" \
  -H "X-Alpha-API-Key: $ALPHA_API_KEY" \
  -d "$(jq -n --arg q "<user-question>" '{query:$q}')"
```

The response is JSON with `matched_nodes` (subjects/platforms/policies/people/decisions/systemic-issues — each with `dri_name`, `dri_email`, `excerpt`, `body`) and `matched_documents` (chat-shared files with filename, date, sender, drive_url).

### 3. For explicit DRI lookup → GET /dri/<area>
```bash
curl -sS -H "X-Alpha-API-Key: $ALPHA_API_KEY" \
  "$ALPHA_API_URL/dri/$(python3 -c 'import urllib.parse,sys;print(urllib.parse.quote(sys.argv[1]))' <area>)"
```

Returns `{area, dri_name, dri_email, node}`.

### 4. For document-only search → GET /documents/search?q=...
```bash
curl -sS -G -H "X-Alpha-API-Key: $ALPHA_API_KEY" \
  --data-urlencode "q=<search terms>" \
  "$ALPHA_API_URL/documents/search"
```

Returns the top 20 matching files with Drive links.

### 5. For "something is wrong / missing" → POST /feedback
When the user runs `/alpha-academic-remote:feedback` or says Claude got something wrong, POST:
```bash
curl -sS -X POST "$ALPHA_API_URL/feedback" \
  -H "Content-Type: application/json" \
  -H "X-Alpha-API-Key: $ALPHA_API_KEY" \
  -d '{"kind":"correction","message":"<what they said>","reported_by":"<who>","claude_said":"<what Claude said>","source":"<source if known>"}'
```

### 6. For proposed new decisions → POST /decisions/pending
```bash
curl -sS -X POST "$ALPHA_API_URL/decisions/pending" \
  -H "Content-Type: application/json" \
  -H "X-Alpha-API-Key: $ALPHA_API_KEY" \
  -d '{"title":"...","context":"...","decision":"...","area":"...","source_of_authority":"..."}'
```

The decision goes to the server-side pending queue and Tripti routes it to the responsible DRI for approval.

## Response format — the three pillars

Every answer must include:

1. **📚 What we know** — quote specific excerpts in this priority order:
   a. From `matched_documents[*].content_excerpt` when `has_content=true` — these are direct quotes from PDFs (playbooks, manuals, announcements) that the API has extracted text for. **These are gold — the actual team policy text, not a paraphrase.** Always prefer them.
   b. From `matched_nodes[*].excerpt` or `matched_nodes[*].body` — the curated knowledge graph (subjects, platforms, policies).
   c. Cite dates when the text includes them.
2. **👤 Who to contact** — name + email from the top-scoring node's `dri_name` / `dri_email`. If the top node has no DRI, fall back to the next node that does.
3. **📄 Where the doc is** — for each relevant document in `matched_documents`, show `filename` (shared `date` by `sender`) and the `drive_url`. Do NOT invent local paths — they don't exist for remote users.

### When to fetch full document content

If `content_excerpt` is too short to answer fully and `has_content=true`, fetch the full text:

```bash
curl -sS -G -H "X-Alpha-API-Key: $ALPHA_API_KEY" \
  --data-urlencode "path=<content_path>" \
  "$ALPHA_API_URL/documents/content"
```

(The `content_path` isn't returned in the /ask response — you'd need to grep the manifest separately. Usually the excerpt is enough.)

### Rendering playbook-style docs

If a matched document is a **playbook, runbook, manual, or guide** (filename hints: "Playbook", "Runbook", "Manual", "Guide", "Announcements"), render its content as a step-by-step checklist, not just a quote. Example:

> The user asks "how do I proctor a test?" → the Mastery Test Proctoring Playbook is matched with `has_content=true`. Parse the excerpt for numbered steps, before/during/after sections, rules, and present them as a skimmable checklist with emoji section headers (🔴 rules, ⏰ before, 🟢 during, 👀 observations, ✅ after).

### Rendering support articles (the 436 articles from `support.alpha.school`)

If a matched document filename starts with a numeric ID like `123406-...md` or contains a line `[Source](https://support.alpha.school/article/<id>)`, it's a support article. **Always render in this 3-part format:**

```
📄 **<Article title>**
🔗 https://support.alpha.school/article/<id>

📝 **Summary** (1–2 sentences in your own words, distilled from the article body — not a quote)

✅ **Quick reply — what to do**
1. <First concrete step from the article's "Next steps" section>
2. <Second step>
3. <Third step>
   …
(Pull these from the numbered list under "Next steps" in the article. If the article has no Next-steps section, derive 2-3 actions from the body. Keep each step to one line.)

ℹ️ **Why** (one line — the underlying reason from the Summary section, if present)
```

**Example.** User asks *"why is reading missing during bracketing?"* → article 123406 matches with `has_content=true`. Response:

> 📄 **Why is Reading missing during bracketing?**
> 🔗 https://support.alpha.school/article/123406
>
> 📝 **Summary**
> When a Reading test identifies Knowledge Gaps under Language, the next Reading test pauses until the student finishes the Language KG lessons. This is intentional — Language skills are foundational for reading comprehension.
>
> ✅ **Quick reply — what to do**
> 1. Open Dash → My Required Learning → Language section
> 2. Complete all assigned Language Knowledge Gap lessons
> 3. Dash will automatically assign the next Reading test once the KGs are done
>
> ℹ️ **Why:** Reading test → Language gap identified → Complete Language lessons → Next Reading test.

### Format for "what do I do if X" (procedural — non-article cases)

Combine matched policy + systemic-issue nodes into step-by-step actions:
- Situation restate (one sentence)
- ✅ Steps to take (ordered, concrete, cheapest-check-first)
- ❌ What NOT to do (from past decisions / known issues)
- 👤 DRI to escalate to
- 📄 Relevant docs

## Rules

- **Never fabricate.** Only use content returned by the API. If a field is null/empty, say "not captured."
- **If the API returns no matched nodes**, tell the user: "I couldn't find that in our knowledge base — try `/alpha-academic-remote:feedback` to flag it for Tripti."
- **Stale check.** If the top node's `last_updated` is >60 days old, prepend: `⚠ Source last updated [date] — verify with the DRI before acting.`
- **Cite dates** from excerpts wherever possible.
- **Don't leak the API key** in any output — it should only appear in the `X-Alpha-API-Key` request header, never in the rendered answer.
