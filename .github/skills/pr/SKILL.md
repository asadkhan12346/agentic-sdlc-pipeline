---
name: pr
description: Prepare traceable release artifacts and follow a human-authorized GitHub PR sequence.
user-invocable: false
---
# PR Release Method

## Stage A: release files
Proceed only when the PR phase is ACTIVE and verify says `Result: PASS`. Write `artifacts/pr.md` with Summary, What changed, Traceability, Test evidence, Risks, Rollback, and Reviewer checklist. Update `CHANGELOG.md` in Keep a Changelog format with Unreleased, Added, Changed, and Fixed. Submit and stop for human approval.

## Stage B: authorized Git sequence
Proceed only in a later invocation after every phase is APPROVED and the human explicitly says `go`. Check `git rev-parse --show-toplevel`, `git remote -v`, and that the current branch is not main/master. Run `git switch -c sdlc/<slug>` with a slug of at most 40 characters. Run `git add` with explicit paths only: `artifacts todo tests/test_todo.py CHANGELOG.md sdlc/state.json sdlc/audit.log.jsonl`; inspect `git diff --cached --name-only`; run `git commit` conventionally; run `git push -u origin <branch>`. Then use `search_pull_requests` to check for an existing PR on that branch; if none, use `create_pull_request` as a DRAFT with the pushed branch as head, the default branch as base, a title from the commit, and the body from `artifacts/pr.md`; then use `pull_request_read` to confirm and report the URL.

## Safety rules
Never use force push, `git add -A`, `git add .`, `--no-verify`, history rewriting, or push to main/master. Do not stage files outside the allowlist. Never merge a pull request. Use no GitHub tool other than `create_pull_request`, `search_pull_requests`, and `pull_request_read`. Do not claim a push or PR succeeded without actual output. Stop on any command error; never retry with a bypass.

## On failure
Report the exact failing command and observed error. Do not change remote settings, authenticate, or edit unrelated files. The human resolves setup problems.

## Templates
PR headings: Summary; What changed; Traceability (each ID to evidence); Test evidence; Risks; Rollback; Reviewer checklist.
Changelog headings: `## [Unreleased]`, then Added, Changed, and Fixed.

## Validation checklist
- Verification PASS and every requirement ID has evidence.
- Stage A outputs match observed changes.
- Stage B has explicit `go`, approved phases, safe branch, expected remote, and exact staged file list.
- No secrets or unrelated files are staged.

## Stop condition
After Stage A submission, stop for the human approval command. After Stage B, stop after reporting the actual PR result or first error.
