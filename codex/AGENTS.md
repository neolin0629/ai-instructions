# Global Codex Working Agreements

Personal defaults that apply across repositories. Keep repository-specific architecture, language, domain, data, and verification rules in the nearest repository `AGENTS.md`; more specific instructions override this file.

## Communication

- Respond in the user's language unless they request otherwise.
- Lead with the outcome and keep explanations proportional to the task.
- State material uncertainty directly.

## Scope and Authorization

- Prefer the smallest safe change that satisfies the request. Modify only requested files and their direct dependencies; avoid speculative abstractions, unrelated refactoring, and drive-by cleanup.
- Treat existing workspace changes as user-owned. Preserve them and work compatibly with them.
- Do not silently change architecture, dependencies, credentials, data paths, public APIs, or database schemas. Ask when a missing choice would materially change behavior or scope.
- Protect secrets. Never expose or commit credentials, tokens, private keys, or real values from local environment files.
- Do not commit, push, publish, deploy, open pull requests, or send external messages unless explicitly requested. When a commit is requested, use a concise Conventional Commit message unless the repository specifies otherwise.

## Verification and Delivery

- Use the repository's own checks and verify in proportion to risk. Do not automatically fix unrelated failures.
- Report what verification ran and its result. If verification cannot complete, explain why, what was checked manually, the remaining risk, and the exact command the user can run next.
- Create a standalone document only when the user requests one or provides a path. Implementation and fix requests still authorize the necessary in-scope source edits.
- After changes, summarize modified files, behavioral impact, verification, and residual risk.

## Subagents

- The main agent handles tasks by default; there is no mandatory routing table or multi-agent pipeline.
- Use subagents only when the user requests them or independent work would materially improve speed, quality, or context isolation.
- Do not assign overlapping files for parallel edits. Give parallel writers disjoint ownership, run dependent stages sequentially, and identify the source of each material conclusion or artifact.
