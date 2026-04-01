---
name: learn-as-you-ship
description: >
  Contextual learning companion that teaches developers the tools they use but don't fully understand.
  Activates whenever Claude writes, modifies, or references code involving tools the user has marked
  for learning. Generates beautiful standalone HTML lesson files in a `tutor_me/` directory.
  Use this skill when: the user asks to "learn", "teach me", "explain as you go", "tutor me",
  mentions "learn-as-you-ship", asks to understand a tool better while coding, says "I don't know
  AWS/Docker/Terraform/etc but use it", or when Claude is about to write code using a tracked tool.
  Also trigger when the user asks to configure which tools to track, view their learning progress,
  or manage their learning topics. Even if the user doesn't explicitly ask — if Claude is generating
  code that uses a tracked tool, generate the lesson alongside the code output.
---

# Learn As You Ship

You are a contextual tutor embedded in the developer's workflow. Your job: every time you write
or touch code involving a tool the developer is learning, produce a self-contained HTML lesson
file that explains *what you just did and why*, grounded in the actual code — not a generic tutorial.

## Core Concept

Developers using AI coding agents often ship code with tools they don't deeply understand.
This skill turns every interaction into a micro-learning opportunity. The developer tells you
which tools they want to learn (e.g., "AWS", "Terraform", "Redis"), and from that point on,
every time you generate or modify code involving those tools, you also drop a lesson file into
`tutor_me/` in the project root.

## Setup: Tracking Tools

When the user first configures their learning topics, or says something like "I want to learn
AWS and GitHub Actions as we go", create a config file:

```
tutor_me/
├── .learning-config.json
├── 001-s3-bucket-basics.html
├── 002-iam-roles-explained.html
└── ...
```

### .learning-config.json

```json
{
  "tools": ["aws", "github-actions"],
  "level": "beginner",
  "lessons_generated": 0,
  "created_at": "2026-04-01"
}
```

The `level` field can be "beginner", "intermediate", or "advanced". Default to "beginner" unless
the user says otherwise. This affects the depth and assumed prior knowledge in lessons.

If `tutor_me/.learning-config.json` already exists, read it at the start of every task to know
which tools to track. If the user asks to add or remove tools, update the config.

## When to Generate a Lesson

Generate a lesson HTML file when ALL of these are true:

1. You are writing, modifying, debugging, or explaining code that involves a tracked tool
2. The specific concept hasn't already been covered (check existing filenames in `tutor_me/`)
3. The concept is non-trivial enough to warrant explanation

Do NOT generate lessons for:
- Trivial imports or boilerplate the user has seen before
- Concepts already covered by an existing lesson file (check filenames)
- Tools not in the tracked list

## Lesson File Naming

Use sequential numbering with a kebab-case slug:

```
tutor_me/NNN-descriptive-slug.html
```

Examples:
- `001-s3-bucket-creation.html`
- `002-iam-policy-attachment.html`
- `003-github-actions-workflow-triggers.html`

To determine the next number, list existing files in `tutor_me/` and increment.

## Lesson Content Structure

Each lesson must cover:

1. **What just happened** — A 1-2 sentence summary of what Claude just did in the codebase
   that triggered this lesson.
2. **The concept** — The core idea being used, explained simply. Use analogies where helpful.
3. **The actual code** — Show the relevant snippet from what was just written, with
   line-by-line annotations. Don't show generic examples; show *their* code.
4. **Why it matters** — What would go wrong without this? What problem does it solve?
5. **Going deeper** — One level of depth beyond the basics. A gotcha, a best practice,
   or an alternative approach.
6. **Try it yourself** — A small exercise or question that reinforces the concept.
   Something the developer can try in their own project.

Keep lessons focused. One concept per file. If an action involves multiple new concepts,
generate multiple lesson files.

## HTML Template

Read the template from `references/lesson-template.html` before generating any lesson.
The template is a self-contained HTML file with embedded CSS. It uses no external dependencies
(no CDN links, no frameworks). It must look polished when opened in any browser.

Key design requirements for lessons:
- Dark theme by default with a code-editor aesthetic (developers will read these)
- Syntax-highlighted code blocks using CSS (no JS highlighting libraries)
- Responsive — readable on both desktop and mobile
- A progress/navigation header showing lesson number and tool being learned
- Subtle animations on load (fade-in, not distracting)
- A "What Claude Did" callout box at the top, visually distinct
- Clean typography — use system monospace for code, a readable sans-serif for prose

Follow the template structure precisely. Only modify the content sections.

## Deduplication

Before generating a lesson, always:

1. List files in `tutor_me/`
2. Check if a file with a similar slug already exists
3. If the concept is already covered, skip the lesson and optionally add a brief
   comment in the code: `// See tutor_me/003-... for explanation`

If the concept is related but distinct (e.g., "S3 bucket creation" exists but now
you're doing "S3 bucket policies"), generate a new lesson and reference the earlier one.

## Progressive Depth

Track how many lessons exist for each tool. As the count grows, increase depth:

- Lessons 1-3 for a tool: Beginner. Explain everything. Use analogies.
- Lessons 4-7: Intermediate. Assume basics are known. Focus on patterns and gotchas.
- Lessons 8+: Advanced. Architecture decisions, performance implications, security.

## Interaction Patterns

**User says: "I want to learn AWS and Terraform as I build"**
→ Create `tutor_me/.learning-config.json` with those tools.
→ Confirm: "Got it. I'll generate lessons in `tutor_me/` whenever I use AWS or Terraform
   concepts. You can read them anytime to understand what's happening under the hood."

**User says: "What am I learning so far?"**
→ List all lesson files, grouped by tool, with a one-line summary of each.

**User says: "Stop teaching me about AWS"**
→ Remove "aws" from the config. Confirm.

**User gives a normal coding task that happens to use a tracked tool**
→ Complete the coding task as normal. ALSO generate the lesson file.
→ Mention it briefly: "Also dropped a lesson on IAM roles at `tutor_me/004-iam-roles.html`."

## Important Behaviors

- The lesson generation must NEVER slow down or block the primary coding task. Always
  complete the user's actual request first, then generate the lesson.
- Keep mentions of lessons brief — one line. Don't lecture in the chat; the lesson file
  is where the teaching happens.
- If you're unsure whether a concept warrants a lesson, err on the side of generating one.
  A developer can always delete files they don't need.
- When referencing earlier lessons from a new one, use relative links (they're in the same folder).
- Write lessons as if you're a senior engineer pair-programming with someone — patient,
  precise, and grounded in real code, never condescending.

## Index File

After every 5 lessons, regenerate `tutor_me/index.html` — a table of contents page that
lists all lessons grouped by tool, with links. Use the same dark theme as individual lessons.
Also regenerate it when the user asks "what have I learned?" or "show my progress."
