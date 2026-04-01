# learn-as-you-ship

> Stop copy-pasting AI code you don't understand.

An agent skill that turns your coding assistant into a contextual tutor. Every time Claude (or another AI agent) writes code using a tool you're learning, it drops a beautiful standalone HTML lesson into `tutor_me/` — explaining *your actual code*, not generic tutorials.

## The Problem

You're building a project using FastAPI + AWS + Redis + Supabase + Nginx + 10 other things. You know Python and FastAPI cold, but AWS and Redis? That's all Claude. You ship working infrastructure you can't debug, deploy pipelines you can't modify, and IAM policies you can't read.

**learn-as-you-ship** fixes this by teaching you in context — from your own codebase, as you build.

## How It Works

1. **Tell Claude what you're learning:**
   ```
   "I want to learn AWS and GitHub Actions as I go"
   ```

2. **Keep coding normally.** Claude does its thing.

3. **Check `tutor_me/` when you have 5 minutes.** Each file is a focused lesson about a concept Claude just used in your code — with your actual code annotated, not generic examples.

```
tutor_me/
├── index.html                          ← Your learning dashboard
├── .learning-config.json               ← Tracked tools config
├── 001-s3-bucket-creation.html         ← What Claude did + why
├── 002-iam-policy-attachment.html
├── 003-github-actions-workflow.html
└── ...
```

## Installation

### For Claude Code (recommended)

```bash
# Install from skills.sh
npx skills add YOUR_GITHUB_USERNAME/learn-as-you-ship

# Or manually: copy to your project
cp -r learn-as-you-ship/ .claude/skills/
```

### For other agents

Copy `SKILL.md` to your agent's skills directory. The skill follows the open SKILL.md standard and works with Cursor, Cline, Codex, Windsurf, and other compatible agents.

## Lesson Design

Each lesson is a self-contained dark-themed HTML file. No dependencies. Opens in any browser. Looks like this:

- **"What just happened"** — One-line summary of what Claude did
- **The Concept** — Core idea explained simply, with analogies
- **Your Code, Annotated** — The actual code from your project, line by line
- **Why This Matters** — What breaks without this
- **Going Deeper** — Gotchas, best practices, alternatives
- **Try It Yourself** — A small exercise to reinforce the concept

Lessons get progressively deeper as you accumulate more for the same tool.

## Configuration

The skill auto-creates `tutor_me/.learning-config.json`:

```json
{
  "tools": ["aws", "terraform", "github-actions"],
  "level": "beginner",
  "lessons_generated": 0,
  "created_at": "2026-04-01"
}
```

Manage it conversationally:
- *"Add Docker to my learning list"*
- *"I already know Terraform basics, set it to intermediate"*
- *"Stop teaching me about Redis"*
- *"What have I learned so far?"*

## Add to .gitignore (optional)

If you don't want lessons in version control:

```
tutor_me/
```

Or keep them — they're great documentation for your future self and teammates.

## License

MIT
