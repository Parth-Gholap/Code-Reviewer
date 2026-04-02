# 🤖 GitHub Multi AI Agent Code Reviewer

An automated multi-agent code review system built with **n8n**, **OpenRouter**, and **GitHub**. Every time a Pull Request is opened, 4 AI agents collaboratively review the code, generate a humanized comment, and post it to GitHub — with a human approval step via Gmail before publishing.

---

## 🔥 Features

- **4 AI Agents** working in sequence to review code changes
- **Multi-model support** via OpenRouter (free tier supported)
- **Human-in-the-loop** approval via Gmail before posting
- **Auto-labeling** of PRs with `ReviewedByAI` or `waitingForHuman`
- **Humanized comments** that look like suggestions from a real developer
- **Merged PR detection** — skips review if PR is already merged

---

## 🏗️ Workflow Architecture

```
GitHub PR Opened
      ↓
HTTP Request (fetch changed files)
      ↓
JavaScript (format diffs)
      ↓
If2 (is PR open?) ──── No ──→ Create Review1 + Label (waitingForHuman)
      ↓ Yes
If1 (PR type check)
      ↓
AI Agent 1 (primary code review)
      ↓
AI Agent 2 (find missed issues)
      ↓
AI Agent 3 (synthesize + humanize)
      ↓
Edit Fields (set finaloutput)
      ↓
Gmail (send for human approval)
      ↓
If (approved?) ──── No ──→ Stop
      ↓ Yes
Create Review (post comment to GitHub)
      ↓
Edit Issue (add ReviewedByAI label)
```

---

## 🛠️ Tech Stack

| Tool | Purpose |
|------|---------|
| [n8n](https://n8n.io) | Workflow automation platform |
| [OpenRouter](https://openrouter.ai) | AI model routing (free tier) |
| GitHub OAuth2 | Trigger on PRs, post reviews |
| Gmail OAuth2 | Human approval step |

---

## ⚙️ Setup Guide

### Prerequisites

- n8n Cloud account (or self-hosted n8n)
- GitHub account
- OpenRouter account (free)
- Gmail account

---

### Step 1 — Import the Workflow

1. Open n8n → click **+ New Workflow**
2. Click the **⋮ menu** → **Import from file**
3. Upload the `Github_multi_AI_agent_code_reviewer.json` file

---

### Step 2 — Configure GitHub OAuth2

1. Go to GitHub → **Settings → Developer Settings → OAuth Apps → New OAuth App**
2. Set the callback URL to:
   ```
   https://oauth.n8n.cloud/oauth2/callback
   ```
3. In n8n → **Settings → Credentials → New → GitHub OAuth2 API**
4. Paste Client ID and Client Secret → click **Connect**

---

### Step 3 — Configure OpenRouter

1. Sign up at [openrouter.ai](https://openrouter.ai) → **Keys → Create Key**
2. In n8n → **Settings → Credentials → New → OpenRouter API**
3. Paste your API key

**Recommended free models:**

| Agent | Model |
|-------|-------|
| AI Agent 1 | `meta-llama/llama-3.3-70b-instruct:free` |
| AI Agent 2 | `meta-llama/llama-3.3-70b-instruct:free` |
| AI Agent 3 | `deepseek/deepseek-r1:free` |
| AI Agent 4 | `deepseek/deepseek-r1:free` |

> Using different models across agents avoids hitting free tier rate limits (20 req/min, 200 req/day).

---

### Step 4 — Configure Gmail OAuth2

1. In n8n → **Settings → Credentials → New → Gmail OAuth2 API**
2. Click **Connect** → authorize via Google popup

---

### Step 5 — Update Repo Details

Open these nodes and point them to your own GitHub repo:

- **GitHub Trigger**
- **Create a review**
- **Edit an issue**
- **Create a review1**
- **Edit an issue1**

Also update the `sendTo` email in the **"Send message and wait for response"** node to your own email.

---

### Step 6 — Create GitHub Labels

In your GitHub repo → **Issues → Labels**, create:

- `ReviewedByAI`
- `waitingForHuman`

---

### Step 7 — Activate

Click **Publish** in n8n to activate the workflow.

---

## 🧪 Testing

1. Open a Pull Request in your configured repo
2. Watch n8n **Executions** tab — all nodes should turn green
3. Check your Gmail inbox for the approval email (check spam if not received)
4. Click the link → select **"Yes"** → submit
5. Check your PR — the AI review comment and `ReviewedByAI` label should appear

---

## ⚠️ Common Issues

| Issue | Fix |
|-------|-----|
| Rate limit error on AI agents | Use different free models across agents, wait 2-3 mins between tests |
| Gmail not received | Check spam folder, mark as not spam |
| Workflow not triggering | Verify webhook in GitHub repo → Settings → Webhooks |
| If node always false | Ensure dropdown label matches exactly between Gmail node and If node |

---

## 📁 Project Structure

```
├── Github_multi_AI_agent_code_reviewer.json   # n8n workflow file
└── README.md                                  # This file
```

---

## 🙌 Credits

Built with ❤️ using n8n, OpenRouter free models, and GitHub webhooks.
