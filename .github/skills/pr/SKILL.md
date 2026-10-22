---
name: pr
description: Prepare traceable release artifacts and follow a human-authorized GitHub PR sequence.
user-invocable: false
---
# PR Release Method

## Stage A: release files
Proceed only when the PR phase is ACTIVE and verify says `Result: PASS`. Write `artifacts/pr.md` with Summary, What changed, Traceability, Test evidence, Risks, Rollback, and Reviewer checklist. Update `CHANGELOG.md` in Keep a Changelog format with Unreleased, Added, Changed, and Fixed. Submit and stop for human approval.

## Stage B: authorized GitHub MCP sequence
Proceed only in a later invocation after every phase is APPROVED, `artifacts/verify.md` says `Result: PASS`, and the human explicitly says `go` in this invocation. Otherwise stop without changing GitHub.

1. Inspect Git state read-only: repository root, `origin` URL, current branch, status, and complete diffs. Derive the GitHub owner/repository from `origin` and require `main` as the PR base. Stop if the intended changes or exact files are ambiguous, or if unrelated, untracked, deleted, runtime-state, or audit changes are present.
2. Limit proposed files to reviewed, intended phase outputs and the Stage A release files `artifacts/pr.md` and `CHANGELOG.md`. Never include `sdlc/`, `hooks/`, `.github/hooks/`, `.git/`, secrets, or unrelated files. Inspect each complete file content before any remote write.
3. Call `get_me` before other GitHub operations. Create a unique `sdlc/<slug>` branch (slug at most 40 characters) from `main` with `create_branch`.
4. Use `push_files` once to commit only the exact reviewed paths and contents, with a conventional commit message. Never use local `git push`, another file-mutation tool, or a fallback if the MCP operation fails. Stop if a deletion is needed or the exact intended result cannot be represented.
5. Use `search_pull_requests` for this branch and base `main`. If a PR already exists, read and report it without making a duplicate. Otherwise use `create_pull_request` with this feature branch as head, `main` as base, a concise title, a traceable description, and `draft: true`.
6. Use `pull_request_read` to confirm the returned head, base, changed files, open/draft state, and URL. Report only observed results. Never merge.

## Safety rules
Never use local `git push`, force push, `git add -A`, `git add .`, `--no-verify`, history rewriting, or write to main/master. Never use GitHub file mutation, deletion, branch-update, issue, or review tools. Never merge. Do not claim a branch, commit, or PR succeeded without actual MCP results. Stop on any error; never retry with a bypass.

## On failure
Report the exact failing command and observed error. Do not change remote settings, authenticate, or edit unrelated files. The human resolves setup problems.

## Templates
PR headings: Summary; What changed; Traceability (each ID to evidence); Test evidence; Risks; Rollback; Reviewer checklist.
Changelog headings: `## [Unreleased]`, then Added, Changed, and Fixed.

## Validation checklist
- Verification PASS and every requirement ID has evidence.
- Stage A outputs match observed changes.
- Stage B has explicit `go`, approved phases, expected repository, `main` base, safe feature branch, and exact reviewed file list/content.
- No secrets or unrelated files are staged.

## Stop condition
After Stage A submission, stop for the human approval command. After Stage B, stop after reporting the actual PR result or first error.