Alpha Academic Plugin — Install Guide
========================================

You've been given an API key and API URL by Tripti. This guide gets you from zero to "asking Claude about Alpha academics" in about 10 minutes. Works on Mac and Windows.

If anything is confusing, message Tripti — she'd rather walk you through it than have you give up.


WHAT THIS IS
------------

A Claude Code plugin that lets you ask Alpha Schools academic questions in plain English. Examples:

   • "who owns Math 3-6?"
   • "what's the proctoring playbook?"
   • "what do I do if a student hits the 3-attempt cap?"
   • "where's the Alpha Mastery Targets sheet?"

It returns: the answer, who to contact, and the relevant Drive link. When the answer comes from a PDF (playbook, runbook, manual, session announcement), Claude quotes the actual content directly — not just a "go read this 8-page PDF" pointer. Backed by 1,300+ docs from our Academics Team chat + every team decision + every DRI's current ownership.


WHAT YOU NEED BEFORE STARTING
------------------------------

1. A Mac or Windows computer (instructions cover both — pick the right section as you go).

2. The two things Tripti DM'd you:
      • ALPHA_API_URL — looks like https://alpha-academic-api.vercel.app
      • ALPHA_API_KEY — a long random string

3. Claude Code installed (we'll check in Step 2).

You do NOT need a GitHub account, code experience, or any "developer tools" beyond Claude Code.


STEP 1 — OPEN TERMINAL
-----------------------

🍎 Mac
   1. Press Cmd + Space (opens Spotlight).
   2. Type "terminal" and press Enter.
   3. A black/white window opens with a $ or % prompt.

🪟 Windows
   1. Press the Windows key or click Start.
   2. Type "terminal" or "powershell" and press Enter.
   3. A blue/black window opens with a > prompt.
      (If "Terminal" doesn't appear in your Start menu, install it free from Microsoft Store: search "Windows Terminal".)


STEP 2 — CHECK IF YOU HAVE CLAUDE CODE
---------------------------------------

Copy-paste into Terminal and press Enter:

   claude --version

   ✅ Prints something like "Claude Code v2.1.97" → you have it. Skip to Step 4.
   ❌ Says "command not found" or "not recognized" → keep going to Step 3.


STEP 3 — INSTALL CLAUDE CODE (skip if Step 2 worked)
-----------------------------------------------------

🍎 Mac
   Paste this and press Enter:

      curl -fsSL https://claude.com/install.sh | bash

   Wait ~1 minute. Then close Terminal completely (Cmd+Q) and reopen it.

🪟 Windows
   You need Node.js first. Check by running:

      node --version

   ✅ Prints a version like "v20.10.0" → you have it. Continue below.
   ❌ Says "not recognized" → install from https://nodejs.org (pick the LTS
      version, run the installer, accept defaults). Then close + reopen Terminal.

   Once Node is installed, run:

      npm install -g @anthropic-ai/claude-code

   Wait ~2 minutes. Close + reopen Terminal.

VERIFY (both platforms): run "claude --version" again — should print a version number.


STEP 4 — SAVE YOUR API KEY PERMANENTLY
---------------------------------------

You need to set two environment variables that persist every time you open Terminal: ALPHA_API_URL and ALPHA_API_KEY.

🍎 Mac

   Open the shell config file in a simple editor:

      nano ~/.zshrc

   Use arrow keys to scroll to the bottom of the file. On a new line, type these three lines (replace the placeholders with what Tripti DM'd you):

      # Alpha Academic API
      export ALPHA_API_URL="https://alpha-academic-api.vercel.app"
      export ALPHA_API_KEY="paste-your-real-key-here"

   Save and exit:
      • Press Ctrl + O, then Enter (save)
      • Press Ctrl + X (exit)

   Reload the file:

      source ~/.zshrc

   Verify both values are set:

      echo $ALPHA_API_URL
      echo $ALPHA_API_KEY

   Both should print real values.

🪟 Windows

   Run these two commands one at a time (replacing placeholders with your real values):

      [Environment]::SetEnvironmentVariable("ALPHA_API_URL", "https://alpha-academic-api.vercel.app", "User")

      [Environment]::SetEnvironmentVariable("ALPHA_API_KEY", "paste-your-real-key-here", "User")

   Close Terminal completely and reopen (Step 1) so it picks up the new variables.

   Verify both values are set:

      echo $env:ALPHA_API_URL
      echo $env:ALPHA_API_KEY

   Both should print real values.


STEP 5 — START CLAUDE CODE
---------------------------

In Terminal (Mac or Windows), type:

   claude

A welcome panel appears with "Welcome back" or "Welcome". You're now inside Claude Code.


STEP 6 — INSTALL THE ALPHA ACADEMIC PLUGIN
-------------------------------------------

Inside Claude Code, paste exactly and press Enter:

   /plugin marketplace add triptikhetan-max/alpha-public

Wait for "Successfully added marketplace: alpha-public".

Then:

   /plugin install alpha-academic-remote@alpha-public

When it asks scope, just press Enter (the default is correct).

Wait for "✓ Installed alpha-academic-remote".


STEP 7 — ASK IT SOMETHING
--------------------------

Type a real question, hit Enter:

   who owns social studies k-8?

You'll see:
   • A "Skill" line being invoked (alpha-academic-remote:ask)
   • A bash command appearing — Claude wants to call the API. Press 1 (Yes) to allow it.
   • After ~2 seconds, you get the answer with:
      📚 What we know
      👤 Who to contact (DRI name + email)
      📄 Where the doc is (Drive link, when relevant)

Done. Now ask it real questions about your work.


SAMPLE QUESTIONS TO TRY
------------------------

   who owns math 3-6?
   where is the alpha mastery targets sheet?
   what's the 3-attempt cap policy?
   what do I do if a student hits the cap on alpharead?
   how do I proctor a mastery test?
   why did we stop using edia?
   who do I contact about a zearn rostering bug?


WHEN IT GETS SOMETHING WRONG
-----------------------------

This plugin is only as good as the knowledge it's built on. When Claude says something wrong or doesn't know, flag it so Tripti can fix it for everyone:

   /alpha-academic-remote:feedback Claude said X but it's actually Y. Source: [where you got the right answer]

Or just tell Tripti directly. She reviews feedback weekly and updates the brain.


TROUBLESHOOTING
----------------

"command not found: claude" / "not recognized as the name of a cmdlet"
   → You need to install Claude Code (Step 3) and fully close + reopen Terminal afterwards.

"command not found: nano" (Mac)
   → Try opening the file in TextEdit instead:
      open -a TextEdit ~/.zshrc
      Edit, save, close. Then "source ~/.zshrc" in Terminal.

echo $ALPHA_API_KEY (Mac) prints an empty line
   → The "source ~/.zshrc" step didn't pick up the changes. Fully close Terminal (Cmd+Q) and reopen.

echo $env:ALPHA_API_KEY (Windows) prints an empty line
   → You didn't fully close + reopen Terminal after running SetEnvironmentVariable. New env vars need a fresh Terminal session.

Plugin install fails with "Permission denied (publickey)"
   → You're trying to install via SSH. The marketplace is public — no GitHub login needed. The exact command is:
         /plugin marketplace add triptikhetan-max/alpha-public

Claude says "MISSING" when I ask a question
   → Your env vars aren't set. Re-do Step 4. Specifically check:
         echo $ALPHA_API_URL          (Mac)
         echo $env:ALPHA_API_URL       (Windows)

I keep getting "401 Unauthorized" when Claude calls the API
   → Your API key is wrong, expired, or got revoked. DM Tripti for a fresh key.

My company laptop blocks installs / curl / npm
   → Two options:
      1. Ask IT to whitelist claude.com, npmjs.com, and vercel.app.
      2. Ask Tripti for a Slack-bot version (coming later) that doesn't need any install.

Anything else
   → Message Tripti. She built this and would rather help you in 2 minutes than have you give up.


PRIVACY
--------

   • The plugin code is public, but the data it queries is private — gated by your API key. Don't share your key with anyone outside the Alpha academics team.
   • Every query is logged anonymously (just for FAQ + gap-tracking — the question text only, no other data). This is how we know what to add to the brain.
   • Student names are scrubbed from the knowledge base. The brain doesn't have FERPA-sensitive content about specific students.
   • If your key ever leaks (laptop stolen, accidentally pasted in a public Slack), tell Tripti immediately and she'll rotate it.


WHAT'S NEXT
------------

Once you've used it for a week, Tripti will check in to ask:
   • Did you find it useful?
   • What did you ask that it couldn't answer?
   • What features would make it more useful?

That feedback drives v2.

— Built by Tripti Khetan, with help from the entire Alpha Academics Team chat history.
