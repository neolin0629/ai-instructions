# Global Codex Working Agreements

Personal defaults that apply across repositories. Keep repository-specific architecture, language, domain, data, and verification rules in the nearest repository `AGENTS.md`; more specific instructions override this file.

## Communication

- Respond in the user's language unless they request otherwise.
- Lead with the outcome and keep explanations proportional to the task.
- State material uncertainty directly.

## Scope and Authorization

- Prefer the smallest safe change that satisfies the request. Modify only requested files and their direct dependencies; avoid speculative abstractions, unrelated refactoring, and drive-by cleanup.
- Treat existing workspace changes as user-owned. Preserve them and work compatibly with them.
- Make necessary changes within the requested scope, including direct dependencies and affected documentation, and explain material changes. Ask only when a missing decision materially affects scope, compatibility, data safety, or external side effects and cannot be inferred from the request or repository.
- Carry authorized work through the requested outcome, not just a first implementation. Resolve failures caused by the change and complete applicable verification before handing back. State low-risk assumptions and continue without repeating authorization requests; while awaiting a necessary decision, continue independent work.
- Review-only, diagnostic, and explanation requests stay read-only unless changes are also requested.
- Protect secrets. Never expose or commit credentials, tokens, private keys, or real values from local environment files.
- Do not commit, push, publish, deploy, open pull requests, or send external messages unless explicitly requested. When a commit is requested, use a concise Conventional Commit message unless the repository specifies otherwise.

## Verification and Delivery

- Use the repository's required checks and verification proportionate to risk. Passing checks completes verification, not any remaining requested deliverables. Repeat or broaden checks only for new changes, failures, or unresolved risks; report unrelated failures without automatically fixing them.
- Report what verification ran and its result. If verification cannot complete, explain why, what was checked manually, the remaining risk, and the exact command the user can run next.
- Return explanations and proposals in chat by default. Create or update files when they are a requested deliverable or necessary to complete the task; choose a conventional path when none is supplied. Avoid unsolicited planning documents.
- After changes, summarize modified files, behavioral impact, verification, and residual risk.

## Skills

- Load only skills and supporting references whose scope matches the current task. Use `architect` for architecture proposals and `product-manager` for requirement briefs; ordinary implementation does not require either. Loading a skill does not require a subagent.
- Read project documentation relevant to the change: architecture for service boundaries, data documentation for schema changes, deployment guidance for releases. Do not require a full repository map or unrelated documents before a small edit.
- Use relevant skills within the user's authorized scope. User instructions take precedence over skill guidelines, subject to higher-priority instructions and enforced permissions. If a skill blocks progress, cite the exact file and instruction and explain the conflict.

## Subagents

- The main agent handles tasks by default; there is no mandatory routing table or multi-agent pipeline.
- Use subagents only when the user requests them or independent work would materially improve speed, quality, or context isolation.
- Give each subagent a bounded task, relevant context, completion criteria, and clear file ownership. Use `code-review` for independent review. Keep parallel write scopes disjoint; integrate dependent results sequentially and identify their source.
