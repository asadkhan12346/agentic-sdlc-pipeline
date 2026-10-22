---
name: pr
description: Prepare release materials and optionally open a PR after explicit human go-ahead.
agent: pr
---
# Phase 8: pr (agent: pr)

STEP 1: Run `python orchestrator.py status`. Stage A requires pr ACTIVE. Stage B requires every phase APPROVED, `Result: PASS` in `artifacts/verify.md`, and the human's explicit `go` in this invocation. Otherwise STOP without changing GitHub.

## Read
Read all approved artifacts, especially `artifacts/verify.md`, and inspect Git state read-only.

## Write (only these files)
- `artifacts/pr.md`
- `CHANGELOG.md`

## Scope
Stage A requires `Result: PASS`, prepares release files, submits, and stops. Stage B is a later invocation after all approvals and explicit `go`. Inspect Git state read-only and derive the owner/repository from `origin`; target `main`. Stop if the branch, PR body, exact changed-file list, or any diff is unclear, or if unrelated, untracked, deleted, runtime-state, or audit changes are present. Never include `sdlc/`, `hooks/`, `.github/hooks/`, `.git/`, secrets, or unrelated files.

Call GitHub `get_me` before other GitHub operations. Create a unique `sdlc/<slug>` branch from `main` with `create_branch`, then use `push_files` once to commit only the exact reviewed paths and contents with a conventional message. Do not use local `git push`, another file-mutation tool, or any fallback if MCP fails. Use `search_pull_requests` to look for an existing PR with that head and base `main`; report an existing PR instead of duplicating it. Otherwise use `create_pull_request` for a DRAFT PR with head set to the feature branch and base `main`. Use `pull_request_read` to verify its head, base, changed files, open/draft state, and URL. Never merge. Never edit `sdlc/`, `orchestrator.py`, `hooks/`, or `.github/hooks/`; never approve, reject, or reopen a phase.

## Finish
Follow the agent procedure and self-check. Stage A runs `python orchestrator.py submit pr` then prints exactly `Awaiting human approval. Run: python orchestrator.py approve pr --by <your-name>` and stops. Stage B must not proceed by asking for or inferring authorization; if `go` was not explicitly given in this invocation, stop. Report actual MCP outcomes and stop at the first error.