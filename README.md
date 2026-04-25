# Alpha Academic — Install Guide for Teammates

You've been given an **API key** and **API URL** by Tripti. This guide gets you from zero to "asking Claude about Alpha academics" in about 10 minutes.

If anything below is confusing, message Tripti — she'd rather walk you through it than have you give up.

---

## What this is

A Claude Code plugin that lets you ask Alpha Schools academic questions in plain English. Examples:

- *"who owns Math 3-6?"*
- *"what's the proctoring playbook?"*
- *"what do I do if a student hits the 3-attempt cap?"*
- *"where's the Alpha Mastery Targets sheet?"*

It returns: the answer, who to contact, and the relevant Drive link. Backed by 1,300+ docs from our Academics Team chat + every team decision + every DRI's current ownership.

---

## What you need before starting

1. **A Mac** (these instructions are Mac-specific. Windows + Linux are also supported but talk to Tripti for setup help.)
2. **The two things Tripti DM'd you:**
   - `ALPHA_API_URL` — looks like `https://alpha-academic-api.vercel.app`
   - `ALPHA_API_KEY` — a long random string like `t_bT5n5KiNSsx9oWcsTC7yZryu8WVfLGB35YEhfTULI`
3. **Claude Code installed** (we'll check below)

You do NOT need:
- A GitHub account
- Any code experience
- Any "developer tools"

---

## Step 1 — Open Terminal

1. Press **Cmd + Space** (this opens Spotlight, the little search bar that appears on screen)
2. Type **`terminal`** and press **Enter**

A black/white window opens with text and a `$` or `%` prompt. That's Terminal. ✅

---

## Step 2 — Check if you have Claude Code already

Copy this, paste into Terminal, press Enter:

```bash
claude --version
```

**Two possible outcomes:**

- ✅ It prints something like `Claude Code v2.1.97` → you have it. Skip to Step 4.
- ❌ It says `command not found: claude` → keep going to Step 3.

---

## Step 3 — Install Claude Code (skip if Step 2 worked)

Copy this, paste into Terminal, press Enter:

```bash
curl -fsSL https://claude.com/install.sh | bash
```

Wait for it to finish (~1 min). Then **close Terminal completely** (Cmd+Q) and reopen it (Step 1 again). This makes the new `claude` command available.

Verify with `claude --version` again. Should now print a version number.

---

## Step 4 — Save your API key permanently

Right now we're going to add your key to a file Terminal reads every time it opens, so you don't have to retype it.

Copy these THREE lines, paste them into Terminal **all at once**, press Enter:

```bash
echo '' >> ~/.zshrc
echo '# Alpha Academic API' >> ~/.zshrc
echo 'export ALPHA_API_URL="PASTE_URL_HERE"' >> ~/.zshrc
echo 'export ALPHA_API_KEY="PASTE_KEY_HERE"' >> ~/.zshrc
```

Now we have to replace the placeholders with your real values:

```bash
nano ~/.zshrc
```

This opens an editor. Use arrow keys to scroll to the bottom. You'll see the lines you just added with `PASTE_URL_HERE` and `PASTE_KEY_HERE`.

Replace `PASTE_URL_HERE` with the URL Tripti gave you (e.g. `https://alpha-academic-api.vercel.app`).
Replace `PASTE_KEY_HERE` with your API key (e.g. `t_bT5n5KiNSsx9oWcsTC7yZryu8WVfLGB35YEhfTULI`).

When done:
1. Press **Ctrl + O** (save) → press **Enter**
2. Press **Ctrl + X** (exit)

Then reload the file:

```bash
source ~/.zshrc
```

Verify it worked:

```bash
echo $ALPHA_API_URL
echo $ALPHA_API_KEY
```

Both should print your values (not `PASTE_..._HERE`). ✅

---

## Step 5 — Start Claude Code

In Terminal, type:

```bash
claude
```

Wait. A nice ASCII-art welcome panel appears with "Welcome back" or "Welcome". You're now inside Claude Code. ✅

---

## Step 6 — Install the Alpha Academic plugin

Inside Claude Code (NOT in regular Terminal), copy and paste this exactly, press Enter:

```
/plugin marketplace add triptikhetan-max/alpha-public
```

Wait for "Successfully added marketplace: alpha-public".

Then:

```
/plugin install alpha-academic-remote@alpha-public
```

When it asks **scope**, just press **Enter** (the default "Install for you" is correct).

Wait for "✓ Installed alpha-academic-remote".

---

## Step 7 — Ask it something

Type a real question, hit Enter:

```
who owns social studies k-8?
```

You'll see:
- A "Skill" line being invoked (alpha-academic-remote:ask)
- A bash command appearing — Claude wants to call the API. **Press 1 (Yes) to allow it.**
- After ~2 seconds, you get the answer in a structured format with:
  - 📚 What we know
  - 👤 Who to contact (DRI name + email)
  - 📄 Where the doc is (Drive link)

🎉 **You're done.** Now ask it real questions about your work.

---

## Sample questions to try

```
who owns math 3-6?
where is the alpha mastery targets sheet?
what's the 3-attempt cap policy?
what do I do if a student hits the cap on alpharead?
how do I proctor a mastery test?
why did we stop using edia?
who do I contact about a zearn rostering bug?
```

---

## When it doesn't know something or gets it wrong

This plugin is only as good as the knowledge it's built on. When Claude says something wrong or doesn't know, **flag it** so Tripti can fix it for everyone:

```
/alpha-academic-remote:feedback Claude said X but it's actually Y. Source: [where you got the right answer]
```

Or just tell Tripti directly. She reviews feedback weekly and updates the brain.

---

## Troubleshooting

### "command not found: claude"

You need to install Claude Code (Step 3) and **fully close + reopen Terminal** afterwards.

### "command not found: nano"

Your Mac is unusual — try `vim ~/.zshrc` instead, or just open the file in TextEdit:

```bash
open -a TextEdit ~/.zshrc
```

Edit there, save, close. Then `source ~/.zshrc` in Terminal.

### `echo $ALPHA_API_KEY` prints an empty line

The `source ~/.zshrc` step didn't pick up the changes. Try **fully closing Terminal** (Cmd+Q) and reopening it (Step 1).

### Plugin install fails with "Permission denied (publickey)"

You're trying to install from GitHub via SSH but don't have keys set up. The public plugin install command is:

```
/plugin marketplace add triptikhetan-max/alpha-public
```

(The marketplace is **public** — no GitHub authentication needed. If you're getting this error, you might have copy-pasted the wrong URL.)

### Claude says "MISSING" when I ask a question

Your env vars aren't set. Re-do Step 4. Specifically check:

```bash
echo $ALPHA_API_URL
echo $ALPHA_API_KEY
```

Both must print real values. If they print blanks, the `source ~/.zshrc` didn't run or the values didn't save in the file.

### I keep getting "401 Unauthorized" when Claude calls the API

Your API key is wrong, expired, or got revoked. DM Tripti for a fresh key.

### Anything else

Message Tripti. She built this and would rather help you in 2 minutes than have you give up.

---

## Privacy

- The plugin code is public, but **the data it queries is private** — gated by your API key. Don't share your key with anyone outside the Alpha academics team.
- Every query you make is logged (anonymously, just for FAQ + gap-tracking — no personal data, no message content beyond the question text). This is how we know what to add to the brain.
- Student names are scrubbed from the knowledge base. The brain doesn't have FERPA-sensitive content about specific students.

If your key ever leaks (laptop stolen, accidentally pasted in a public Slack), tell Tripti immediately and she'll rotate it.

---

## What's next

Once you've used it for a week, Tripti will check in to ask:
- Did you find it useful?
- What did you ask that it couldn't answer?
- What features would make it more useful?

That feedback drives v2.

— Built by Tripti Khetan, with help from the entire Alpha Academics Team chat history.
