# GitHub Copilot Power Hour
**1-Hour Team Training | Presenter Notes + Demo Script**

---

## ⏱ Timing Overview

| Segment | Topic | Time |
|--------|-------|------|
| 0:00 | Intro & framing | 3 min |
| 0:03 | Multi-agent orchestration | 15 min |
| 0:18 | CLI vs. IDE — when & how | 12 min |
| 0:30 | GitHub repos crash course + MCP | 12 min |
| 0:42 | VS Code user-level settings | 8 min |
| 0:50 | Tips, tricks & hidden gems | 7 min |
| 0:57 | Q&A | 3 min |

---

## 0:00 — Intro & Framing (3 min)

**Key message:** Copilot isn't just autocomplete anymore. It's a platform.

> 💬 *"Today we're going to show you how to use Copilot as a system — not just a suggestion engine. By the end of this hour you'll have concrete settings, workflows, and mental models you can use starting today."*

**Talking points:**
- The gap between "I have Copilot" and "I use Copilot well" is huge
- This hour is about closing that gap
- Mix of demo + hands-on — follow along if you want

---

## 0:03 — Multi-Agent Orchestration (15 min)

### What is it? (2 min)

Copilot can now run **multiple specialized agents in sequence or in parallel** to complete a complex task — each agent handles a focused sub-task and passes context to the next.

```
User request
     │
     ▼
┌─────────────┐     ┌──────────────┐     ┌────────────────┐
│ Explore     │────▶│  Code edit   │────▶│  Test runner   │
│ agent       │     │  agent       │     │  agent         │
│ (research)  │     │  (implement) │     │  (verify)      │
└─────────────┘     └──────────────┘     └────────────────┘
```

**Why it matters:**
- Single prompts hit context limits on large tasks
- Specialists outperform generalists on focused sub-tasks
- You can parallelize independent work streams

---

### How to trigger it — 3 patterns (8 min)

#### Pattern 1: Let Copilot decide (implicit orchestration)
In VS Code Chat, just describe a complex task. Copilot will invoke tools automatically:

```
# Example prompt
"Refactor the auth module: find all usages, update them to use
the new JWT interface, then run the tests and fix any failures."
```
👉 **Demo:** Show a multi-step task in VS Code Chat where you can see tool calls happening in the sidebar (search → edit → terminal)

---

#### Pattern 2: Explicit tool chaining in the CLI

```bash
# Chain: research → implement → verify
gh copilot explain "how does our rate limiter work"
# Then use that context in your next prompt
gh copilot suggest "add retry logic to the HTTP client based on the rate limiter pattern"
```

---

#### Pattern 3: Copilot Coding Agent (async, GitHub-native)
Assign issues directly to Copilot from GitHub.com or CLI:

```bash
# From CLI — assign an issue to copilot
gh issue edit 42 --add-assignee "@copilot"

# Or from the issue page: Assignees → Copilot
```

Copilot opens a PR, runs CI, iterates on failures — all without you watching.

**Best practices for coding agent tasks:**
- Write issues like you'd brief a junior dev (context, constraints, acceptance criteria)
- Use labels to scope: `copilot`, `good first issue`
- Review the PR diff, not the process — Copilot shows its work in the PR description

---

### 🔑 Key mental model (2 min)

> **"Think in workflows, not prompts."**

| Instead of... | Try... |
|--------------|--------|
| One giant prompt | Break into research → implement → verify |
| Re-explaining every time | Use `#codebase` or `#file` to ground context |
| Blocking on Copilot | Assign to coding agent, review async |

---

## 0:18 — CLI vs. IDE (12 min)

### The core question: *Where am I working?*

| Situation | Best tool |
|-----------|-----------|
| Editing code, need inline suggestions | **IDE (VS Code)** |
| Already in terminal, need a quick answer | **CLI (`gh copilot`)** |
| Long multi-file refactor | **IDE Chat** |
| Running commands you half-remember | **CLI `suggest`** |
| Reviewing a PR, quick context lookup | **CLI** |
| Writing a feature from scratch | **IDE + Coding Agent** |

---

### CLI Deep Dive (5 min)

Install / verify:
```bash
gh extension install github/gh-copilot
gh copilot --version
```

**Two main commands:**

```bash
# 1. suggest — "what command do I need?"
gh copilot suggest "find all files modified in the last 7 days larger than 10MB"

# 2. explain — "what does this command do?"
gh copilot explain "awk '{print $2}' | sort | uniq -c | sort -rn"
```

**Pro tip — shell alias:**
```bash
# Add to ~/.zshrc or ~/.bashrc
alias cops='gh copilot suggest'
alias cope='gh copilot explain'

# Now:
cops "compress a directory excluding node_modules"
```

**Interactive mode flags:**
```bash
gh copilot suggest -t shell   # force shell command context
gh copilot suggest -t git     # git command context
gh copilot suggest -t gh      # GitHub CLI context
```

👉 **Demo:** Live terminal — suggest a complex git command, then explain a scary-looking one-liner

---

### IDE Deep Dive (5 min)

**Chat modes — know the difference:**

| Mode | Trigger | Best for |
|------|---------|---------|
| Inline chat | `Cmd+I` | Quick edits, explain selection |
| Panel chat | `Cmd+Shift+I` or sidebar | Multi-turn conversations |
| Edits mode | `Cmd+Shift+P` → "Copilot Edits" | Multi-file changes |

**Context is everything — use `#` references:**
```
# In chat:
@workspace "Find all API endpoints that don't have auth middleware" 
#file:src/routes/users.ts "Why is this endpoint returning 403?"
#codebase "How is logging configured?"
```

**Participants you should know:**
- `@workspace` — full repo awareness
- `@vscode` — VS Code settings & commands questions
- `@terminal` — terminal context
- `@github` — PRs, issues, code search (requires sign-in)

👉 **Demo:** Use `@workspace` to answer a cross-file question, then switch to Edits mode to make the change

---

## 0:30 — GitHub Repos Crash Course + MCP (12 min)

### Navigating repos like a pro (4 min)

**Keyboard shortcuts on GitHub.com:**
```
T          → fuzzy file finder
.          → open repo in github.dev (VS Code in browser)
Cmd+K      → command palette (search everything)
```

**Powerful search syntax:**
```
# Search within a repo
org:your-org language:typescript is:open                    # open TS files
path:src/api extension:ts "async function"                  # code search
repo:your-org/repo-name created:>2024-01-01 is:pr merged    # recent PRs
```

**Clone the right way:**
```bash
# SSH (preferred — no password prompts)
git clone git@github.com:org/repo.git

# Or via gh CLI (handles auth automatically)
gh repo clone org/repo

# Fork + clone in one command
gh repo fork org/repo --clone
```

---

### Personal repos as your Copilot config layer (3 min)

> Your personal GitHub account is a **superpower** for Copilot customization.

**What to put in a personal repo:**
```
my-copilot-config/
├── .github/
│   └── copilot-instructions.md   ← your persistent Copilot persona
├── prompts/
│   ├── code-review.md            ← reusable prompt templates  
│   ├── pr-description.md
│   └── standup-summary.md
└── settings/
    └── vscode-settings.json      ← your portable VS Code config
```

**`.github/copilot-instructions.md` example:**
```markdown
You are a senior TypeScript engineer. Always:
- Prefer `const` over `let`
- Add JSDoc to exported functions
- Suggest error boundaries for React components
- Flag any `any` types with a comment
```

This file is **automatically loaded** when Copilot opens any repo you own.

---

### MCP (Model Context Protocol) Enablement (5 min)

MCP lets Copilot reach **outside the codebase** — into Jira, Slack, databases, internal tools.

**What it unlocks:**
```
Without MCP:  Copilot knows your code
With MCP:     Copilot knows your code + your tickets + your docs + your data
```

**Enable MCP in VS Code:**

1. `Cmd+Shift+P` → "Open User Settings (JSON)"
2. Add your MCP server config:

```json
{
  "github.copilot.chat.mcp.enabled": true,
  "mcp": {
    "servers": {
      "github": {
        "command": "npx",
        "args": ["-y", "@modelcontextprotocol/server-github"],
        "env": {
          "GITHUB_PERSONAL_ACCESS_TOKEN": "${env:GITHUB_TOKEN}"
        }
      },
      "filesystem": {
        "command": "npx",
        "args": ["-y", "@modelcontextprotocol/server-filesystem", "/Users/yourname/projects"]
      }
    }
  }
}
```

3. Reload window → Copilot now has GitHub API access

**Quick win MCP servers to try first:**
| Server | What it adds |
|--------|-------------|
| `@modelcontextprotocol/server-github` | Issues, PRs, code search via API |
| `@modelcontextprotocol/server-filesystem` | Read files outside workspace |
| `@modelcontextprotocol/server-postgres` | Query your dev database in chat |
| `@modelcontextprotocol/server-slack` | Read channel context |

👉 **Demo:** Add GitHub MCP server, then ask `@github "summarize the last 5 merged PRs"`

---

## 0:42 — VS Code User-Level Settings (8 min)

### Configure once, get everywhere

**Open user settings:** `Cmd+Shift+P` → "Open User Settings (JSON)"

This file lives at `~/Library/Application Support/Code/User/settings.json` (macOS) and applies to **every project and every chat session**.

---

### The Copilot settings that matter most

```json
{
  // ── Copilot Core ──────────────────────────────────────────
  "github.copilot.enable": {
    "*": true,
    "plaintext": false,     // turn off in plain text files
    "markdown": true
  },

  // ── Inline suggestions ────────────────────────────────────
  "editor.inlineSuggest.enabled": true,
  "github.copilot.editor.enableAutoCompletions": true,

  // ── Chat behavior ─────────────────────────────────────────
  "github.copilot.chat.localeOverride": "en",
  "github.copilot.chat.followUps": "always",      // suggest follow-up questions
  "github.copilot.chat.generateTests.codeLensTitle": "Generate Tests",

  // ── Coding agent / edits ──────────────────────────────────
  "github.copilot.chat.edits.enabled": true,
  "github.copilot.chat.agent.runTasks": true,      // let agent run npm/build tasks

  // ── Instruction files (repo + personal) ───────────────────
  "github.copilot.chat.codeGeneration.useInstructionFiles": true,
  "github.copilot.chat.codeGeneration.instructions": [
    {
      "text": "Always add error handling. Prefer async/await over .then(). Use TypeScript strict mode."
    }
  ],

  // ── Workspace context ─────────────────────────────────────
  "github.copilot.chat.indexing.enabled": true,   // full codebase indexing

  // ── MCP ───────────────────────────────────────────────────
  "github.copilot.chat.mcp.enabled": true
}
```

---

### Instruction files — the multiplier

You can set **global instructions** that apply to every chat, plus override per-repo:

```
Priority order (highest to lowest):
1. Inline instructions in the chat prompt
2. .github/copilot-instructions.md  (repo-level)
3. User settings instructions array (global)
```

**Add your team's global baseline in user settings:**
```json
"github.copilot.chat.codeGeneration.instructions": [
  {
    "text": "We use conventional commits. Always suggest commit messages in the format: type(scope): description"
  },
  {
    "text": "Our stack: React 18, TypeScript strict, Vitest, Tailwind. Use these, not alternatives."
  }
]
```

👉 **Demo:** Show the settings file, add one instruction, ask Copilot to write a commit message — before and after

---

## 0:50 — Tips, Tricks & Hidden Gems (7 min)

### 🚀 Top 10 team productivity boosts

**1. `/fix`, `/explain`, `/tests`, `/doc` slash commands**
```
# In any chat or inline prompt:
/fix              → fix the selected code
/explain          → explain what it does
/tests            → generate tests for selection
/doc              → add documentation
/new              → scaffold a new file/component
```

**2. `#selection` is your best friend**
Select code, open chat, start with `#selection` to ground the conversation.

**3. Commit message generation**
In Source Control panel → click the sparkle icon ✨ next to the message box.

**4. PR description generation**
When creating a PR in VS Code or on GitHub.com → "Copilot" button auto-fills description from diff.

**5. Terminal explain on errors**
After a failed command in VS Code terminal → click the sparkle ✨ next to the error to get an instant explanation + fix.

**6. Rename symbol with context**
`F2` to rename → Copilot suggests semantically appropriate names based on usage.

**7. `@workspace /new` for project scaffolding**
```
@workspace /new  "create a React component for a data table with sorting and pagination"
```

**8. Notebook support**
Copilot works in Jupyter notebooks — great for data exploration and documentation.

**9. Voice input (VS Code Insiders)**
`Cmd+Alt+V` → speak your prompt — useful for longer descriptions.

**10. The "Kebab menu" in chat**
Every chat response has `···` → "Insert into Terminal", "Copy", "Insert at Cursor" — use these instead of copy-paste.

---

### 🎯 Workflow: The "Morning Standup" flow

```bash
# In terminal — summarize what you did yesterday
gh copilot suggest "show my git commits from yesterday across all repos"

# Or in VS Code chat:
"@github summarize my merged PRs and closed issues from the last 24 hours"
```

### 🎯 Workflow: The "Rubber Duck" review

Before submitting a PR, ask Copilot to review your own diff:
```
# In chat with your diff open:
"Review this diff for: security issues, missing error handling, 
performance problems, and anything that will get flagged in review."
```

### 🎯 Workflow: The "Onboarding Accelerator"

When joining a new repo:
```
@workspace "Give me a tour of this codebase: 
- What does it do?
- What are the main modules?
- How do I run it locally?
- Where would I add a new API endpoint?"
```

---

## 0:57 — Q&A (3 min)

**Common questions to prep for:**

> *"Is Copilot sending my code to the cloud?"*  
Copilot uses your code as context for suggestions. Enterprise plans include IP indemnity and data privacy controls. Check your org's policy.

> *"How do I make it stop suggesting things mid-typing?"*  
`Cmd+Shift+P` → "Copilot: Toggle Inline Suggestions" or set `"github.copilot.editor.enableAutoCompletions": false`

> *"Can I use Copilot offline?"*  
No — Copilot requires internet access (it calls GitHub's API).

> *"What's the difference between Copilot Individual, Business, and Enterprise?"*  
Individual: personal use, basic features. Business: org management, audit logs, policy controls. Enterprise: all of Business + fine-tuned models, Copilot Knowledge Bases, stricter data controls.

---

## 📎 Resources & Links

| Resource | URL |
|---------|-----|
| Copilot Docs | https://docs.github.com/en/copilot |
| MCP Servers registry | https://github.com/modelcontextprotocol/servers |
| VS Code Copilot settings reference | https://code.visualstudio.com/docs/copilot/reference |
| `gh copilot` extension | https://github.com/github/gh-copilot |
| Copilot Instructions guide | https://docs.github.com/en/copilot/customizing-copilot/adding-custom-instructions |

---

## 🗂 Quick-Reference Card (print / share)

```
SHORTCUTS
─────────────────────────────────────────
Cmd+I           Inline chat
Cmd+Shift+I     Chat panel
Tab             Accept suggestion
Escape          Dismiss suggestion
Alt+]           Next suggestion
Alt+[           Previous suggestion

CLI
─────────────────────────────────────────
gh copilot suggest "..."    Get a command
gh copilot explain "..."    Explain a command
gh issue edit 42 --add-assignee @copilot   Assign to agent

CHAT CONTEXT
─────────────────────────────────────────
@workspace      Full repo context
@github         GitHub PRs/Issues/Search
@terminal       Current terminal state
#file:path      Attach specific file
#selection      Use selected code
#codebase       Semantic code search

SLASH COMMANDS
─────────────────────────────────────────
/fix    /explain    /tests    /doc    /new
```
