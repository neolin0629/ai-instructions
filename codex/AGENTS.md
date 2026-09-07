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
- Carry authorized work through completion. State low-risk assumptions and continue; do not ask again for authorization already given in the session. If a decision is needed, continue independent work while awaiting the answer.
- Review-only, diagnostic, and explanation requests stay read-only unless changes are also requested.
- Protect secrets. Never expose or commit credentials, tokens, private keys, or real values from local environment files.
- Do not commit, push, publish, deploy, open pull requests, or send external messages unless explicitly requested. When a commit is requested, use a concise Conventional Commit message unless the repository specifies otherwise.

## Verification and Delivery

- Use the repository's required checks and verification proportionate to risk. Once they pass, stop unless new changes, failures, or unresolved risks justify more checks. Do not automatically fix unrelated failures.
- Report what verification ran and its result. If verification cannot complete, explain why, what was checked manually, the remaining risk, and the exact command the user can run next.
- Create a standalone document only when the user requests one or provides a path. Implementation and fix requests authorize necessary source edits and updates to existing documentation within scope.
- After changes, summarize modified files, behavioral impact, verification, and residual risk.

## Skills

- Use the `architect` and `product-manager` skills in the main conversation for design and requirements. Loading a skill does not require spawning a subagent; use the `code-review` agent when independent review is needed.
- Use relevant skills within the user's authorized scope. User instructions take precedence over skill guidelines, subject to higher-priority instructions and enforced permissions. If a skill blocks progress, cite the exact file and instruction and explain the conflict.

## Subagents

- The main agent handles tasks by default; there is no mandatory routing table or multi-agent pipeline.
- Use subagents only when the user requests them or independent work would materially improve speed, quality, or context isolation.
- Do not assign overlapping files for parallel edits. Give parallel writers disjoint ownership, run dependent stages sequentially, and identify the source of each material conclusion or artifact.
