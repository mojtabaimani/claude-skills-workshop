---
marp: true
---

# Claude Code Skills Workshop

## Build Custom Slash Commands for Your AI Workflow

---

# About This Workshop

- **Audience**: Developers new to Claude Code
- **Goal**: Learn to create and use custom Skills (SKILL.md)
- **Format**: Concepts + Live Demos + Hands-on Practice

---

# Agenda

1. What are Skills?
2. Skills vs other Claude Code features
3. Creating your first Skill
4. SKILL.md anatomy (frontmatter + body)
5. Skill scopes and discovery
6. Dynamic context injection
7. Supporting files and composition
8. Advanced patterns
9. Best practices
10. Hands-on exercises

---

# Part 1: What Are Skills?

---

# What is a Skill?

A **markdown file** (SKILL.md) that teaches Claude new capabilities.

- Custom slash commands you define yourself (e.g. `/deploy`, `/review`)
- Loaded **on-demand**, not every session
- Can be invoked manually (`/my-skill`) or automatically by Claude
- Think of it as a **recipe** Claude follows for a specific task

---

# The Problem Skills Solve

Without Skills, you describe complex workflows every time:

```
You: "Review this PR. Check for security issues,
      test coverage, code quality, performance.
      Use gh CLI to fetch the diff. Format feedback
      as critical/warning/suggestion..."

...again next week...

You: "Review this PR. Check for security issues..."
```

With a Skill, you just type: **`/code-review 42`**

---

# What Can Skills Do?

**Reference Skills** (Knowledge)
- API conventions, coding patterns
- Domain-specific guidelines
- Legacy system documentation

**Task Skills** (Workflows)
- Deployment pipelines
- Code review checklists
- Test generation
- Documentation generation

---

# Part 2: Skills vs Other Features

---

# Feature Comparison

| Feature | Purpose | Written By | Loaded When |
|---|---|---|---|
| **CLAUDE.md** | Session-wide instructions | You | Every session |
| **Auto Memory** | Learned preferences | Claude | Every session |
| **Skills** | Reusable task workflows | You | On-demand |
| **Hooks** | Guaranteed shell actions | You | On tool events |
| **Permissions** | Access control | You | Always enforced |

---

# When to Use What

| Scenario | Use |
|---|---|
| "Always use 2-space indentation" | CLAUDE.md |
| "Claude learned I prefer vitest" | Auto Memory |
| "Review PRs with this checklist" | **Skill** |
| "Deploy following these 5 steps" | **Skill** |
| "Block commits without tests" | Hook |
| "Never allow rm -rf" | Permissions |

**Key difference**: CLAUDE.md is always loaded (costs tokens).
Skills load only when needed.

---

# Part 3: Creating Your First Skill

---

# Skill File Structure

A skill is a **directory** with a `SKILL.md` file inside:

```
.claude/skills/
  hello/
    SKILL.md          <-- Required: the skill definition
```

That's it. One file, one folder.

---

# Minimal Skill Example

**`.claude/skills/hello/SKILL.md`**:

```markdown
---
name: hello
description: Greet the user and summarize the current project
---

# Hello Skill

Say hello to the user. Then:
1. Read the project's package.json or equivalent
2. Give a brief summary of what this project does
3. List the available npm scripts (or equivalent commands)
```

Now type `/hello` in Claude Code and it works.

---

# Live Demo: Create a Skill

```bash
# 1. Create the skill directory
mkdir -p .claude/skills/explain

# 2. Create SKILL.md
cat > .claude/skills/explain/SKILL.md << 'EOF'
---
name: explain
description: Explain a file or function in simple terms
argument-hint: [filepath]
---

Explain $ARGUMENTS in simple terms that a junior developer
would understand. Include:
1. What it does (one sentence)
2. How it works (step by step)
3. Why it's written this way
4. Any gotchas or edge cases
EOF

# 3. Use it
# In Claude Code: /explain src/auth/login.ts
```

---

# Part 4: SKILL.md Anatomy

---

# Two Parts: Frontmatter + Body

```markdown
---                              # ┐
name: deploy                     # │ YAML Frontmatter
description: Deploy to env       # │ (configuration)
argument-hint: [environment]     # │
disable-model-invocation: true   # │
allowed-tools: Bash, Read        # ┘
---

# Deploy Workflow              # ┐
                                 # │ Markdown Body
Deploy to $ARGUMENTS:            # │ (instructions for Claude)
1. Run tests                     # │
2. Build the app                 # │
3. Push to server                # ┘
```

---

| Frontmatter Field | Default | Description |
|---|---|---|
| `name` | Directory name | Slash command name (`/name`) |
| `description` | First paragraph | What it does; when to use it |
| `argument-hint` | none | Shows in autocomplete: `[env]` |
| `disable-model-invocation` | `false` | Prevent Claude from auto-invoking |
| `user-invocable` | `true` | Show in `/` menu |
| `allowed-tools` | Inherit all | Restrict tool access |
| `model` | Inherit | `sonnet`, `opus`, `haiku` |
| `context` | Main | `fork` for isolated subagent |
| `agent` | `general-purpose` | Subagent type when forked |
| `paths` | All | Glob patterns for auto-loading |

---

# The `description` Field Matters

Claude uses `description` to decide when to auto-invoke a skill.

```yaml
# Bad: too vague
description: A tool for reviewing

# Good: specific with keywords
description: >
  Review code for quality, security, and best practices.
  Use when reviewing pull requests or analyzing code.
```

- Max 250 characters (truncated after that)
- Front-load the key use case
- Include keywords users would naturally say

---

# Arguments with `$ARGUMENTS`

Users pass arguments after the slash command:

```
/deploy production
/review 42
/migrate SearchBar React Vue
```

Access them in your skill body:

| Variable | Value for `/migrate SearchBar React Vue` |
|---|---|
| `$ARGUMENTS` | `"SearchBar React Vue"` |
| `$ARGUMENTS[0]` or `$0` | `"SearchBar"` |
| `$ARGUMENTS[1]` or `$1` | `"React"` |
| `$ARGUMENTS[2]` or `$2` | `"Vue"` |

---

# Arguments Example

```yaml
---
name: migrate-component
description: Migrate a component between frameworks
argument-hint: [component] [from] [to]
---

Migrate the **$0** component from **$1** to **$2**.

Steps:
1. Read the current $1 implementation of $0
2. Understand its props, state, and behavior
3. Rewrite it using $2 conventions
4. Preserve all existing tests
5. Update imports in consuming files
```

Usage: `/migrate-component SearchBar React Vue`

---

# Part 5: Skill Scopes and Discovery

---

# Three Skill Scopes

| Scope | Location | Use case |
|---|---|---|
| **Personal** | `~/.claude/skills/my-skill/` | Your toolkit across all projects |
| **Project** | `.claude/skills/my-skill/` | Team workflows, committed to git |
| **Plugin** | `plugin/skills/my-skill/` | Distributed extensions |

Higher scope wins when names conflict.

---

# Personal vs Project Skills

**Personal** (`~/.claude/skills/`):
- Your own productivity tools
- Available in every project
- Not shared with team

**Project** (`.claude/skills/`):
- Team-specific workflows
- Committed to git for everyone
- Consistent across the team

```
# Personal: your own shortcuts
~/.claude/skills/quick-test/SKILL.md

# Project: team deployment workflow
.claude/skills/deploy/SKILL.md
```

---

# Monorepo Discovery

Skills in nested `.claude/skills/` directories are auto-discovered:

```
monorepo/
|-- .claude/skills/
|   |-- lint/SKILL.md              # Available everywhere
|-- packages/
|   |-- frontend/
|   |   |-- .claude/skills/
|   |       |-- react-review/SKILL.md  # Only in frontend/
|   |-- backend/
|       |-- .claude/skills/
|           |-- api-check/SKILL.md     # Only in backend/
```

---

# Part 6: Dynamic Context Injection

---

# Injecting Live Data

Use `` !`command` `` to inject shell command output into your skill:

```yaml
---
name: pr-summary
description: Summarize the current pull request
allowed-tools: Bash(gh *)
---

## Current PR

Diff:
!`gh pr diff`

Comments:
!`gh pr view --comments`

Changed files:
!`gh pr diff --name-only`

Summarize this pull request. Focus on what changed and why.
```

Commands run **before** Claude sees the prompt. The output replaces the placeholder.

---

# Dynamic Context Examples

**Inject git status:**
```markdown
Current branch state:
!`git status --short`
!`git log --oneline -5`
```

**Inject environment info:**
```markdown
Node version: !`node --version`
Current packages: !`cat package.json`
```

**Inject API data:**
```markdown
Open issues: !`gh issue list --limit 10`
```

This is **preprocessing**, not something Claude executes.

---

# Part 7: Supporting Files

---

# Skills Can Have Multiple Files

```
.claude/skills/code-review/
|-- SKILL.md                # Main skill definition
|-- checklist.md            # Detailed review checklist
|-- examples.md             # Good/bad code examples
|-- security-rules.md       # Security-specific rules
|-- scripts/
    |-- check-coverage.sh   # Helper script
```

Reference them from SKILL.md:

```markdown
For the full checklist, see [checklist.md](checklist.md)
For examples, see [examples.md](examples.md)
Run: `bash ${CLAUDE_SKILL_DIR}/scripts/check-coverage.sh`
```

---

# Built-in Variables

| Variable | Description |
|---|---|
| `$ARGUMENTS` | User-provided arguments |
| `$0`, `$1`, `$2`... | Individual arguments |
| `${CLAUDE_SESSION_ID}` | Current session ID |
| `${CLAUDE_SKILL_DIR}` | Path to this skill's directory |

`${CLAUDE_SKILL_DIR}` is especially useful for referencing scripts and files bundled with the skill.

---

# Part 8: Advanced Patterns

---

# Forked Context (Subagents)

Run a skill in an **isolated context** so it does not pollute your main conversation:

```yaml
---
name: deep-research
description: Research a topic thoroughly
context: fork
agent: Explore
allowed-tools: Read, Grep, Glob, WebSearch, WebFetch
---

Research $ARGUMENTS thoroughly:
1. Search the codebase for related code
2. Read all relevant files
3. Search the web for best practices
4. Return a comprehensive summary
```

The subagent works independently and returns a summary.

---

# When to Use `context: fork`

**Use fork when:**
- The task produces verbose output (research, analysis)
- You want strict tool restrictions
- The work is self-contained

**Stay in main context when:**
- You need quick, targeted work
- The result feeds into the ongoing conversation
- Low latency matters

---

# Preventing Auto-Invocation

For skills with **side effects** (deploy, send messages, delete things):

```yaml
---
name: deploy
description: Deploy application to production
disable-model-invocation: true
---
```

Claude will never auto-invoke this. Users must explicitly type `/deploy`.

---

# Background Knowledge Skills

Skills that teach Claude without appearing in the `/` menu:

```yaml
---
name: legacy-payment-system
description: >
  How the legacy payment system works.
  Use when working with payment code.
user-invocable: false
paths: "src/payments/**"
---

# Legacy Payment System

The payment system uses a two-phase commit...
```

Claude loads this automatically when working in `src/payments/`, but users cannot call `/legacy-payment-system`.

---

# Tool Restrictions

Grant only the tools a skill needs:

```yaml
---
name: safe-explorer
description: Safely explore codebase without modifications
allowed-tools: Read, Grep, Glob
---
```

```yaml
---
name: github-ops
description: GitHub operations
allowed-tools: Bash(gh *), Read
---
```

This prevents accidental file modifications or dangerous commands.

---

# Path-Scoped Skills

Auto-load skills only for matching files:

```yaml
---
name: react-patterns
description: React component patterns and conventions
paths: "**/*.{tsx,jsx}"
---

When writing React components:
- Use functional components with hooks
- Co-locate styles with components
- Extract custom hooks for shared logic
```

Claude loads this only when working with `.tsx` or `.jsx` files.

---

# Extended Thinking

Add "ultrathink" in skill content for complex analysis:

```yaml
---
name: architecture-review
description: Review system architecture decisions
context: fork
---

Using ultrathink, analyze this architecture for:
- Scalability bottlenecks
- Single points of failure
- Security vulnerabilities
- Cost optimization opportunities

Provide a detailed report with recommendations.
```

---

# Part 9: Best Practices

---

# Writing Good Skills

1. **Keep SKILL.md under 500 lines**
   Move detailed docs to supporting files

2. **Write specific descriptions**
   Claude uses them to decide when to auto-invoke

3. **Use `disable-model-invocation: true` for side effects**
   Deploys, sends, deletes should be explicit only

4. **Restrict tools with `allowed-tools`**
   Grant only what the skill needs

5. **Use `context: fork` for heavy tasks**
   Keeps your main conversation clean

---

# Common Mistakes

| Mistake | Fix |
|---|---|
| Vague description | Be specific with keywords |
| Too broad description | Claude invokes when you don't expect |
| No tool restrictions | Skill can accidentally modify files |
| Giant SKILL.md (1000+ lines) | Split into supporting files |
| Side effects without `disable-model-invocation` | Claude might auto-deploy |
| Personal skills in project scope | Use `~/.claude/skills/` for personal tools |

---

# Skill Design Checklist

Before shipping a skill, ask:

- Does the `description` clearly say when to use it?
- Is `argument-hint` set if it takes arguments?
- Are side effects protected with `disable-model-invocation`?
- Are tools restricted to only what's needed?
- Is SKILL.md under 500 lines?
- Would `context: fork` make sense for this task?
- Is it in the right scope (personal vs project)?

---

# Part 10: Hands-on Exercises

---

# Exercise 1: Create a Simple Skill

Create a skill that explains code to beginners.

```bash
mkdir -p .claude/skills/explain
```

Write `.claude/skills/explain/SKILL.md`:
- Takes a file path as argument
- Explains what the code does in simple terms
- Includes a "key concepts" section

Test it: `/explain src/index.ts`

---

# Exercise 2: Create a Review Skill

Create a code review skill with a checklist.

```bash
mkdir -p .claude/skills/review
```

Your `SKILL.md` should:
- Use `allowed-tools: Read, Grep, Glob` (read-only)
- Include a review checklist (security, performance, style)
- Format output as Critical / Warning / Suggestion
- Use `context: fork` to keep main context clean

Test it: `/review src/auth/`

---

# Exercise 3: Create a Skill with Dynamic Context

Create a skill that summarizes recent git activity.

```bash
mkdir -p .claude/skills/git-summary
```

Your `SKILL.md` should:
- Use `` !`git log --oneline -10` `` for recent commits
- Use `` !`git diff --stat` `` for current changes
- Ask Claude to summarize what's been happening

Test it: `/git-summary`

---

# Exercise 4: Create a Personal Skill

Create a personal productivity skill at `~/.claude/skills/`.

Ideas:
- `/todo` that reads TODO comments from your codebase
- `/standup` that summarizes your recent git commits
- `/quick-test` that finds and runs relevant tests
- `/doc` that generates docs for a function

---

# Quick Reference

---

# Cheat Sheet

```
Skill Locations:
  ~/.claude/skills/<name>/SKILL.md      Personal (all projects)
  .claude/skills/<name>/SKILL.md        Project (shared via git)

Key Frontmatter:
  name                  Slash command name
  description           When to use (max 250 chars)
  argument-hint         Autocomplete hint: [arg]
  allowed-tools         Tool restrictions
  disable-model-invocation  true = manual only
  user-invocable        false = background knowledge
  context               fork = isolated subagent
  paths                 Glob patterns for auto-loading

Dynamic Substitutions:
  $ARGUMENTS / $0 $1    User arguments
  ${CLAUDE_SKILL_DIR}   Skill directory path
  ${CLAUDE_SESSION_ID}  Session ID
  !`command`            Inject command output
```

---

# Resources

- **Skills docs**: https://docs.anthropic.com/en/docs/claude-code/skills
- **Plugins docs**: https://docs.anthropic.com/en/docs/claude-code/plugins
- **Claude Code docs**: https://docs.anthropic.com/en/docs/claude-code
- **GitHub**: https://github.com/anthropics/claude-code

---

# Thank You!

## Questions?

A good skill is **focused, well-described, and safely scoped**.

Start simple: one SKILL.md, one task. Build from there.
