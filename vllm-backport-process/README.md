# vLLM Backport Triage Skill

Cursor agent skill for triaging and managing vLLM upstream backport candidates for RHAIIS product releases.

## What this does

This skill teaches the Cursor agent how to:
- Evaluate upstream vLLM fixes against product selection criteria
- Run the 8-step backport triage workflow (identify, dedup, stage, pre-screen, accept/reject, Jira, triage versions, deliver)
- Apply product relevance filters to avoid proposing irrelevant fixes
- Route accepted fixes to the correct backport workflow (stable cherry-pick PR vs. future/EA consolidated Jira)

## Installation

Copy this directory into your Cursor skills folder:

```bash
# Personal (available across all projects)
cp -r vllm-backport-triage ~/.cursor/skills/

# Or project-level (shared via repo)
cp -r vllm-backport-triage your-project/.cursor/skills/
```

## Prerequisites

- Access to INFERENG Jira project
- Clone of the [vllm-runtime](https://github.com/redhat-et/vllm-runtime) repo (contains triage scripts and staging CSV)
- Jira MCP and Slack MCP configured in Cursor (for automated search and issue management)

## Files

| File | Purpose |
|------|---------|
| [SKILL.md](SKILL.md) | Core skill -- selection criteria, process checklist, Jira conventions, product relevance filters, fix categories, backport workflows |
| [reference.md](reference.md) | Lookup tables -- release matrix, version mapping, supported configs, staging CSV schema, rejection log |
