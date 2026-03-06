---
name: vllm-backport-triage
description: Triage and manage vLLM upstream backport candidates for RHAIIS product releases. Use when identifying backport candidates, evaluating upstream fixes, creating Jira issues for backports, or running the bi-weekly backport triage cycle.
---

# vLLM Backport Triage

## Selection criteria

A fix qualifies for backport if it meets **any** of these on a supported model+config:

1. **Customer escalation or support ticket**
2. **Security CVE** -- Critical or High severity
3. **Crash or data corruption** -- on a supported model+config (not theoretical or exotic-only)
4. **Performance regression >10%** -- measurable throughput or latency regression on a supported config

If none of these are met, do **not** propose the candidate unless engineering specifically requests it.

## Reporting templates

### Backport candidate (fix exists)

- **Backport candidate:** [PR link]
- **Fixes:** [one-line description of the bug]
- **Affects:** [which models/configs are impacted, e.g. "3.3 and earlier, EP+MXFP4 config"]

### Critical issue (no fix yet)

- **Critical issue (no upstream fix):** [Jira and GitHub issue link or description]
- **Impact:** [who is affected -- customer name, model, config]
- **Reproducer:** [link to support ticket, Slack thread, or steps to reproduce]
- **Workaround:** [any known workaround, or "none"]

What happens next:
1. Jira issue created in INFERENG with label `upstream-open`
2. Affects Version set based on known affected vLLM version
3. When upstream merges a fix, convert to a normal backport candidate

## Process checklist

Copy and track progress:

```
Backport Triage Cycle:
- [ ] 1. Identify candidates from GitHub issues (label: bug) and release notes (last 2 weeks)
- [ ] 2. Jira dedup: search INFERENG for each candidate; skip closed issues; add labels to open ones
- [ ] 3. Stage: fill Product_Relevance and Fix_Category for each row; skip None/code-quality
- [ ] 4. Engineering pre-screen: post uncertain candidates (Product_Relevance = Low) in Slack
- [ ] 5. Accept/reject: mark Accept Y/N; fill Rejection_Reason for N; update Rejection Log
- [ ] 6. Centralize in Jira: create new issues or add labels to existing open ones
- [ ] 7. Triage versions: run suggest_affected_versions.py; set Affects/Fix Versions
- [ ] 8. Deliver: cherry-pick PRs (stable) or append to consolidated Jira (future/EA)
```

### Step details

**Step 1 -- Identify:** Scan [vllm-project/vllm](https://github.com/vllm-project/vllm/) issues (label `bug`, last 14 days) and [release notes](https://github.com/vllm-project/vllm/releases) (last 2-4 releases). Record `Upstream_vLLM_Version` for each fix. Run `scripts/fetch_backport_candidates.py` to auto-generate the staging CSV.

**Step 2 -- Jira dedup:** Search INFERENG by PR number or keywords. Fill `Existing_Jira` column.
- Existing issue **open** -> add labels `backport` + `vllm`. Do not create a new ticket.
- Existing issue **Closed/Done** -> skip (set Accept = N, Rejection_Reason = "already closed: INFERENG-XXXX").
- No match -> continue to step 3.

**Step 3 -- Stage:** For each remaining row, evaluate:
- `Product_Relevance` (High / Low / None) using the product relevance checklist below
- `Fix_Category` using the fix category table below
- Skip rows where `Product_Relevance = None` or `Fix_Category = code-quality`

**Step 4 -- Engineering pre-screen:** For `Product_Relevance = Low` candidates, post max 5 items in the engineering Slack channel. Engineers reply thumbs-up/down.

**Step 5 -- Accept/reject:** When Accept = N, fill `Rejection_Reason` and add to the [Rejection Log](reference.md#rejection-log).

**Step 6 -- Centralize in Jira:** New issues: project INFERENG, type Bug, labels `vllm` + `backport`, Team = `INFERENG Midstream`. Existing open issues: add labels and update versions.

**Step 7 -- Triage versions:** Run `python scripts/suggest_affected_versions.py v0.15.0` with the upstream fix version.

**Step 8 -- Deliver:** Route to the correct workflow (see Backporting workflow below).

## Jira conventions

| Field | Value |
|-------|-------|
| Project | INFERENG |
| Issuetype | Bug |
| Labels | `vllm`, `backport`; `upstream-open` if no fix yet |
| Team | `INFERENG Midstream` |
| Version naming | RHAIIS-3.3, RHAIIS-3.4 EA-1, RHAIIS-3.4 EA-2, RHAIIS-3.4 |

### Deduplication rules

- **Always search INFERENG first** before creating any issue (by PR number, keywords, error message).
- **One Jira per fix/theme.** Link related GitHub issues as reproducers in a single issue.
- If duplicates exist, close one and keep the canonical issue.

## Product relevance checklist

Before proposing any candidate, all three must pass. If all are "no" or "unclear," mark `Product_Relevance: None` and skip.

1. **Supported models** -- Does it affect a model architecture the product ships? See [reference.md](reference.md#supported-product-configurations).
2. **Supported configurations** -- Does it affect hardware/TP/PP/quantization customers actually use? (A100/H100/H200 CUDA, MI300X ROCm; FP8/AWQ/GPTQ)
3. **Customer reproducibility** -- Has any customer or internal QE hit this? Is it plausible on product-supported configs?

**Example:** PR #34507 "Fused MoE int32 overflow crash with large models" -- sounds severe, but no shipped model triggers this path. Engineering verdict: "zero effect to product." Classified `Product_Relevance: None`, `Fix_Category: code-quality`, filtered out.

## Fix category

Assign one category per candidate. This drives whether to propose it.

| Category | Description | Propose? |
|----------|-------------|----------|
| **customer-facing-bug** | Crashes, wrong results, API errors on supported configs | Yes |
| **security** | CVEs, token leaks, auth bypasses | Yes |
| **performance** | Measurable throughput/latency regression on supported models | Yes |
| **code-quality** | Defensive hardening, edge-case guards not affecting supported configs | **No** |
| **feature-gap** | Missing support for a model/feature customers need | Yes (if customer-driven) |

**Rule:** `code-quality` fixes are not proposed unless engineering requests them. When in doubt, classify conservatively as code-quality and skip.

## Backporting workflow by release type

### Stable releases (3.2, 3.3)

1. Cherry-pick upstream commit(s) against the midstream branch
2. Open PR with: **what it fixes**, **upstream PR link**, **test command**
3. Midstream team reviews, tests, merges, releases

### Future/EA releases (3.4 EA1, EA2, Stable 3.4)

1. Append the PR link to the **consolidated Jira** for that release
2. **One Jira per release per cycle.** Midstream team handles the rest.
3. z-stream release (e.g. 3.4.1) will include backports after stable 3.4 GA

### Process cadence

- Runtime team creates consolidated Jira tickets **bi-weekly** for future releases
- Direct PR creation for stable release fixes
- Midstream team provides branch access to Neural Magic team members

## Scripts (in vllm-runtime repo)

```bash
# Generate staging CSV from GitHub releases + issues
python3 scripts/fetch_backport_candidates.py

# Static staging CSV (no network)
python3 scripts/export_backport_staging.py

# Suggest affected RHAIIS versions for a given upstream fix version
python3 scripts/suggest_affected_versions.py v0.15.0
```

Staging CSV: `docs/backport_staging.csv` (14 columns). See [reference.md](reference.md#staging-csv-schema) for column definitions.

## Additional resources

- [reference.md](reference.md) -- Release support matrix, version mapping, supported configs, staging CSV schema, rejection log
- Upstream repo: [vllm-project/vllm](https://github.com/vllm-project/vllm/) (label: `bug`)
- Upstream releases: [vllm-project/vllm/releases](https://github.com/vllm-project/vllm/releases)
