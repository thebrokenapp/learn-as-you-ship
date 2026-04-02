# learn-as-you-ship

> Stop copy-pasting AI code you don't understand.

An agent skill that turns your coding assistant into a contextual tutor. Every time Claude (or another AI agent) writes code using a tool you're learning, it drops a beautiful standalone HTML lesson into `tutor_me/` — explaining *your actual code*, not generic tutorials.

## The Problem

You're building a project using FastAPI + AWS + Redis + Supabase + Nginx + 10 other things. You know Python and FastAPI cold, but AWS and Redis? That's all Claude. You ship working infrastructure you can't debug, deploy pipelines you can't modify, and IAM policies you can't read.

**learn-as-you-ship** fixes this by teaching you in context — from your own codebase, as you build.

## How It Works

1. **Install the skill.**
2. **Keep coding normally.** Whenever you express uncertainty — "I don't know GitHub well", "what does this Terraform block do?", "can you explain this AWS config?" — Claude generates a lesson alongside its normal response.
3. **Check `tutor_me/` when you have 5 minutes.** Each file is a focused lesson about a concept Claude just used in your code — with your actual code annotated, not generic examples.

```
tutor_me/
├── index.html                          ← Your learning dashboard
├── 001-s3-bucket-creation.html         ← What Claude did + why
├── 002-iam-policy-attachment.html
├── 003-github-actions-workflow.html
└── ...
```

No configuration needed. No setup step. It just works.

## Installation

### For Claude Code

```bash
# Install from skills.sh
npx skills add thebrokenapp/learn-as-you-ship

# Or manually: clone to your project's skills directory
git clone https://github.com/thebrokenapp/learn-as-you-ship.git .claude/skills/learn-as-you-ship
```

You can also invoke it explicitly anytime with `/learn-as-you-ship`.

### For other agents

Copy `SKILL.md` to your agent's skills directory. The skill follows the open SKILL.md standard and works with Cursor, Cline, Codex, Windsurf, and other compatible agents.

## Lesson Design

Each lesson is a self-contained dark-themed HTML file. No dependencies. Opens in any browser. Includes:

- **"What just happened"** — One-line summary of what Claude did
- **The Concept** — Core idea explained simply, with analogies
- **Your Code, Annotated** — The actual code from your project, line by line
- **Why This Matters** — What breaks without this
- **Going Deeper** — Gotchas, best practices, alternatives
- **Try It Yourself** — A small exercise to reinforce the concept

Lessons get progressively deeper as you accumulate more for the same tool.

## Usage

The skill detects learning opportunities automatically from how you talk:

- *"I don't know AWS well but we need an S3 bucket"* → lesson generated
- *"What does this Terraform block do?"* → lesson generated
- *"I don't understand this GitHub Actions config"* → lesson generated
- *"Is this IAM policy right?"* → lesson generated

You can also invoke it directly:

```
/learn-as-you-ship What is this Redis caching pattern doing in our code?
```

Manage your lessons conversationally:

- *"What have I learned so far?"* → lists all lessons with summaries
- *"Stop teaching me about Redis"* → stops generating lessons for that tool
- *"Delete all lessons"* → clears the tutor_me/ directory

## Security

Code snippets in lessons are automatically redacted — API keys, tokens, secrets, and credentials are replaced with `***REDACTED***` before being written to HTML files. Environment variable references (e.g., `os.environ["AWS_SECRET_KEY"]`) are safe to show as-is.

## License

MIT
