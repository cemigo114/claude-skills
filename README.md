# claude-skills

Claude skills for boring PM work -- from making slides to Jira ticket ops.

## What are Claude Skills?

Skills are structured markdown files that teach AI coding agents (Claude in Cursor, Claude Code, etc.) how to perform specific workflows. Instead of re-explaining your process every conversation, you install a skill once and the agent follows it automatically.

Each skill lives in its own folder with a `SKILL.md` (or `.skill` file) that defines **when** to activate and **what** to do.

## Skills in this repo

| Folder | What it does |
|--------|-------------|
| [vllm-backport-process](vllm-backport-process/) | Triage upstream vLLM fixes for RHAIIS product releases -- selection criteria, 8-step process, Jira conventions, product relevance filters |
| [make-fancy-slides](make-fancy-slides/) | Generate Red Hat branded PowerPoint slides from markdown content |

## Installation

### Cursor

Copy a skill folder into your Cursor skills directory:

```bash
# Clone this repo
git clone https://github.com/cemigo114/claude-skills.git

# Install a specific skill (personal, works across all projects)
cp -r claude-skills/vllm-backport-process ~/.cursor/skills/

# Or install at project level (shared via your project repo)
cp -r claude-skills/vllm-backport-process your-project/.cursor/skills/
```

### Claude Code

Copy a skill folder into your project or home directory:

```bash
cp -r claude-skills/vllm-backport-process ~/.claude/skills/
```

Skills are picked up automatically. The agent reads the `SKILL.md` and follows the workflow when the task matches the skill's description.

## Contributing

1. **Fork** this repo
2. **Create a folder** for your skill (use kebab-case, e.g. `my-new-skill/`)
3. **Add a `SKILL.md`** with frontmatter:
   ```markdown
   ---
   name: my-new-skill
   description: What it does. Use when [trigger conditions].
   ---

   # My New Skill

   ## Step 1 ...
   ```
4. **Add a `README.md`** in your folder explaining prerequisites and what the skill does for humans browsing the repo
5. **Optional:** Add a `reference.md` for detailed lookup tables the agent loads on demand
6. **Open a PR** with a short description of the workflow your skill automates

### Skill guidelines

- Keep `SKILL.md` under 500 lines -- concise and actionable
- The `description` field must say **what** the skill does and **when** to use it
- Use progressive disclosure: core workflow in `SKILL.md`, detailed tables in `reference.md`
- No secrets or credentials in skill files
