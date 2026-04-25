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

1. **A Mac or Windows computer** (instructions cover both — pick the right section as you go)
2. **The two things Tripti DM'd you:**
   - `ALPHA_API_URL` — looks like `https://alpha-academic-api.vercel.app`
   - `ALPHA_API_KEY` — a long random string like `t_bT5n5KiNSsx9oWcsTC7yZryu8WVfLGB35YEhfTULI`
3. **Claude Code installed** (we'll check in Step 2)

You do NOT need:
- A GitHub account
- Any code experience
- Any "developer tools" beyond Claude Code

---

## Step 1 — Open Terminal

### 🍎 Mac
1. Press **Cmd + Space** (this opens Spotlight, the little search bar)
2. Type **`terminal`** and press **Enter**
3. A black/white window opens with text and a `$` or `%` prompt. ✅

### 🪟 Windows
1. Press the **Windows key** (bottom-left of keyboard) **or** click the Start button
2. Type **`terminal`** or **`powershell`** and press **Enter**
3. A blue/black window opens with a `>` prompt. ✅
4. *(If "Terminal" doesn't appear in your Start menu, install it free from the Microsoft Store: search "Windows Terminal" — only takes 30 seconds.)*

---

## Step 2 — Check if you have Claude Code already

Copy this, paste into Terminal, press **Enter**:

```
claude --version
```

**Two possible outcomes** (same on Mac and Windows):

- ✅ It prints something like `Claude Code v2.1.97` → you have it. Skip to **Step 4**.
- ❌ It says `command not found` (Mac) or `not recognized as the name of a cmdlet` (Windows) → keep going to **Step 3**.

---

## Step 3 — Install Claude Code (skip if Step 2 worked)

### 🍎 Mac

Copy and paste, press Enter:

```
curl -fsSL https://claude.com/install.sh | bash
```

Wait ~1 min. **Close Terminal completely (Cmd + Q) and reopen** (Step 1 again). This makes the new `claude` command available.

### 🪟 Windows

You need **Node.js** first. Check by running:

```
node --version
```

- ✅ Prints a version like `v20.10.0` → you have it. Continue below.
- ❌ Says "not recognized" → install Node.js from https://nodejs.org (pick the **LTS** version, run the installer, accept defaults). Then **close Terminal and reopen**.

Once Node is installed, run:

```
npm install -g @anthropic-ai/claude-code
```

Wait ~2 min. **Close Terminal and reopen**.

### Verify (both platforms)

Run `claude --version` again. Should print a version number now. ✅

---

## Step 4 — Save your API key permanently

You need to set two environment variables that persist every time you open Terminal: `ALPHA_API_URL` and `ALPHA_API_KEY`.

### 🍎 Mac

Open the shell config file in a simple editor:

```
nano ~/.zshrc
```

Use **arrow keys** to scroll to the bottom of the file. On a new line, type these three lines (replace the placeholder values with what Tripti DM'd you):

```
# Alpha Academic API
export ALPHA_API_URL="https://alpha-academic-api.vercel.app"
export ALPHA_API_KEY="paste-your-real-key-here"
```

Save and exit:
1. Press **Ctrl + O** → press **Enter** (save)
2. Press **Ctrl + X** (exit)

Reload the file:

```
source ~/.zshrc
```

Verify both values are set:

```
echo $ALPHA_API_URL
echo $ALPHA_API_KEY
```

Both should print real values. ✅

### 🪟 Windows

Run these two commands one at a time (replacing placeholders with your real values):

```
[Environment]::SetEnvironmentVariable("ALPHA_API_URL", "https://alpha-academic-api.vercel.app", "User")
```

```
[Environment]::SetEnvironmentVariable("ALPHA_API_KEY", "paste-your-real-key-here", "User")
```

This stores them in your user profile, persistent across all future Terminal sessions.

**Close Terminal completely and reopen** (Step 1) so it picks up the new variables.

Verify both values are set:

```
echo $env:ALPHA_API_URL
echo $env:ALPHA_API_KEY
```

Both should print real values. ✅

---

## Step 5 — Start Claude Code

In Terminal (Mac or Windows), type:

```
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

### "command not found: claude" (Mac) / "not recognized" (Windows)

You need to install Claude Code (Step 3) and **fully close + reopen Terminal** afterwards.

### "command not found: nano" (Mac)

Your Mac is unusual — try `vim ~/.zshrc` instead, or open the file in TextEdit:

```
open -a TextEdit ~/.zshrc
```

Edit, save, close. Then `source ~/.zshrc` in Terminal.

### `echo $ALPHA_API_KEY` (Mac) prints an empty line

The `source ~/.zshrc` step didn't pick up the changes. **Fully close Terminal (Cmd + Q) and reopen** (Step 1).

### `echo $env:ALPHA_API_KEY` (Windows) prints an empty line

You didn't fully close + reopen Terminal after running the `SetEnvironmentVariable` commands. New env vars need a fresh Terminal session.

### Plugin install fails with "Permission denied (publickey)"

You're trying to install via SSH but don't have keys set up. The public plugin install command is:

```
/plugin marketplace add triptikhetan-max/alpha-public
```

(The marketplace is **public** — no GitHub authentication needed. If you're getting this error, you might have copy-pasted the wrong URL.)

### Claude says "MISSING" when I ask a question

Your env vars aren't set. Re-do Step 4. Specifically check:

```
echo $ALPHA_API_URL          (Mac)
echo $env:ALPHA_API_URL       (Windows)
```

Both must print real values. If they print blanks, the env vars didn't save or you need to restart Terminal.

### I keep getting "401 Unauthorized" when Claude calls the API

Your API key is wrong, expired, or got revoked. DM Tripti for a fresh key.

### My company laptop blocks installs / curl / npm

Some IT setups block these. Two options:
1. Ask your IT team to whitelist `claude.com`, `npmjs.com`, and `vercel.app`.
2. Ask Tripti for a Slack-bot version (coming later) that doesn't need any install.

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
