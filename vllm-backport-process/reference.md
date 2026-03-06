# Backport Triage Reference

## Release support matrix

| Release | vLLM Version | Support Window | Backport Method |
|---------|-------------|----------------|-----------------|
| **3.2** | v0.10.1.1 | Until March 2027 | Cherry-pick PR against `midstream-0.10.1.1` branch |
| **3.3** | v0.13.0 | 7 months (from Feb 2026) | Cherry-pick PR against `main` branch |
| **3.4 EA1** | v0.14.1 | No ongoing support | Jira cherry-pick list |
| **3.4 EA2** | v0.16.0 | No ongoing support | Jira cherry-pick list |
| **3.4 Stable** | TBD | Standard support after GA | Jira (pre-GA); z-stream 3.4.1+ (post-GA) |

- EA releases have no ongoing support after initial release.
- After stable 3.4 GA, backports go into z-stream releases (3.4.1, 3.4.2, ...) managed by Midstream via Jira.
- vLLM releases every 2 weeks upstream.

## Version mapping (RHAIIS to vLLM)

- **CSV file:** `RHAI Release to Component Version Mapping - 2026.csv` in the vllm-runtime repo.
- **Row:** `vLLM [CUDA, ROCM, CPU]` -- columns are RHAIIS releases (3.2, 3.3, 3.4 EA1, ...), cell values are vLLM versions.
- **Script:** `python scripts/suggest_affected_versions.py v0.15.0` -- outputs which RHAIIS versions are affected by a fix in the given upstream version.
- For unfixed bugs, use the reporter's vLLM version to get Affects Version (versions shipping that or older vLLM).

## Supported product configurations

### Model architectures by release

| Release | Supported architectures |
|---------|------------------------|
| **3.2** | Llama 2/3/3.1, Mistral/Mixtral, Granite 3.0/3.1, GPT-OSS (20B), Phi-3/3.5, BART (custom) |
| **3.3** | All 3.2 + Llama 3.2/3.3, Granite 3.2, DeepSeek V2/V3, Llama-4 Scout/Maverick, Qwen 2.5 |
| **3.4 EA1** | All 3.3 + DeepSeek V3.1, Qwen 3 |
| **3.4 EA2** | All 3.4 EA1 (+ additions TBD) |

### [TBA] Hardware/CUDA versions supported to validate fixes



### [TBA] Deployment configurations supported to validate fixes



## Staging CSV schema

The staging file `docs/backport_staging.csv` has 14 columns:

| Column | Description |
|--------|-------------|
| Title | Short name of the candidate (e.g. "CUDA compatibility path (PR #33992)") |
| GitHub_Link | URL of the upstream GitHub issue or PR |
| Slack_Link | Optional Slack thread URL for reproducer context |
| Type | `fix_ready` (upstream has a fix) or `unfixed` (no fix yet) |
| Upstream_vLLM_Version | vLLM release containing the fix (e.g. `v0.15.0`); auto-filled from release tags |
| Product_Relevance | High / Low / None -- from product relevance checklist |
| Fix_Category | customer-facing-bug / security / performance / code-quality / feature-gap |
| Suggested_Versions | RHAIIS versions to consider; run suggest_affected_versions.py |
| Reproducers | Related issues/threads to link in Jira |
| Existing_Jira | INFERENG key if issue already exists; Closed = skip, Open = add labels only |
| Accept | Y = create Jira, N = skip |
| Rejection_Reason | Fill when Accept = N (e.g. "zero product effect", "already closed: INFERENG-1234") |
| Jira_Key | Fill after creating the issue (e.g. INFERENG-1234) |
| Notes | Extra context (impact, state, action items) |

## Rejection log

Track engineering rejections to improve the triage criteria over time. When a candidate is rejected, add a row here and fill `Rejection_Reason` in the staging CSV.

| Candidate | Rejection reason | Criteria gap | Date |
|-----------|-----------------|--------------|------|
| Fused MoE int32 overflow crash (PR #34507) | Zero effect to product; only small impact to code simplicity | No product-relevance filter; "crash" keyword treated as high-severity without checking if product models trigger the code path | 2026-03 |

Review this log periodically to tighten the selection criteria.
